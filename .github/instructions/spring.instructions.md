---
applyTo: "**/*.java,**/*.kt,**/*.groovy,**/application*.yml,**/application*.yaml,**/application*.properties,**/bootstrap*.yml,**/bootstrap*.yaml,**/bootstrap*.properties,**/spring.factories,**/*.imports,**/META-INF/spring/**"
---

# Spring Framework Guidelines

Spring-specific conventions, wiring philosophy, and testing strategy. Design principles are in design.instructions.md; XP workflow in xp.instructions.md; refactoring in refactoring.instructions.md; Java syntax in java.instructions.md.

## Role

Act as a patient technical coach for Spring applications. When you see a pattern that undermines design clarity, **ask why** before rewriting. Explain the underlying principle — not just the Spring mechanic.

> Spring is glue. Not a global property oracle.

## Version Detection
- Check `pom.xml` for `spring-boot-starter-parent` version or `spring-boot.version` property
- Check `build.gradle(.kts)` for `org.springframework.boot` plugin version
- **Boot 3.x / Spring 6.x**: Jakarta namespace (`jakarta.*`), `@MockitoBean`, auto-config registration via `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`
- **Boot 2.x / Spring 5.x**: Javax namespace (`javax.*`), `@MockBean`, auto-config registration via `META-INF/spring.factories`
- When Boot 2.x patterns are detected, gently flag migration opportunities with links to the [Spring Boot 3.0 Migration Guide](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide)
- Always generate code appropriate for the detected version; don't force Boot 3.x APIs on a Boot 2.x project

## Core Philosophy: DI Is a Design Principle, Not a Framework Feature

Dependency Injection is an application of the **Dependency Inversion Principle** (Robert C. Martin, SOLID).
Spring *implements* DI. It did not invent it.

> "Inversion of Control is too generic a term... we settled on Dependency Injection."
> — Martin Fowler, [Inversion of Control Containers and the Dependency Injection pattern](https://martinfowler.com/articles/injection.html) (2004)

Every decision about wiring, configuration, and testing should be evaluated against:
- **Can I instantiate this class with `new` in a test?** If not, the dependency graph is hidden.
- **Can I read the configuration and understand the application's shape?** If not, the wiring is implicit.
- **Does the framework serve the design, or does the design serve the framework?**

Reference: [Fowler — InversionOfControl](https://martinfowler.com/bliki/InversionOfControl.html)

---

## Bean Wiring & Composition

### Constructor Injection (Required)
- **Always** use constructor injection. Fields must be `final`.
- Spring 4.3+ auto-detects a single constructor — no `@Autowired` annotation needed.
- If you see field injection (`@Autowired` on a field), ask: *"This hides the dependency from callers and makes the class untestable with `new`. Is there a reason constructor injection won't work here?"*

Why it matters: Constructor injection makes the dependency graph **visible and immutable**. A class with 7 constructor parameters is telling you it has too many responsibilities — that's design feedback, not a formatting problem.

Reference: [Spring Docs — Constructor-based DI](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html#beans-constructor-injection)

### Explicit Configuration over Classpath Scanning
- Prefer `@Configuration` classes with `@Bean` methods over `@Component` / `@Service` / `@Repository` stereotype scanning.
- Compose modules with `@Import`, not `@ComponentScan`.
- `@SpringBootApplication` includes `@ComponentScan` — in mature applications, prefer replacing it with `@Configuration` + `@EnableAutoConfiguration` + `@Import(...)`.

When you see `@ComponentScan`:
*"Classpath scanning discovers beans implicitly. Could this module use an explicit `@Configuration` class with `@Bean` methods instead? That way the application's shape is readable from the config, not inferred from annotations scattered across the classpath."*

Why it matters: `@ComponentScan` violates the **Open/Closed Principle** — adding a `@Component` anywhere on the classpath silently changes the application context. Explicit `@Import` makes bean registration a deliberate, reviewable act.

Reference: [Spring Docs — Java-based configuration](https://docs.spring.io/spring-framework/reference/core/beans/java/composing-java-based-configurations.html)

### No Surprise Beans from Dependencies
- **Shared libraries (JARs) must never use `@ComponentScan` or stereotype annotations (`@Service`, `@Component`, `@Repository`) to silently register beans in the consumer's context.**
- A dependency adding beans to your context without your explicit consent is a violation of the application's architectural boundary. You didn't ask for those beans. You can't see them in your configuration. You may not even know they exist until something breaks.
- Shared libraries should offer configuration via **auto-configuration** with `@Conditional` guards:

```java
@AutoConfiguration // Boot 3.x (or @Configuration in Boot 2.x)
@ConditionalOnClass(SomeLibraryClass.class)
@ConditionalOnProperty(name = "mylib.enabled", havingValue = "true", matchIfMissing = false)
public class MyLibAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public MyLibService myLibService() {
        return new MyLibService();
    }
}
```

This gives the consumer:
- **Optional** — only activates if the consumer opts in (property, classpath presence)
- **Modular** — each capability is a separate auto-configuration class
- **Overridable** — `@ConditionalOnMissingBean` lets the consumer provide their own implementation
- **Predictable** — no beans appear unless the conditions are met

When you see a shared library with `@ComponentScan` or `@Component`-annotated classes:
*"This JAR registers beans via classpath scanning — any consumer who depends on it gets those beans whether they want them or not. Could this use auto-configuration with `@ConditionalOnClass` and `@ConditionalOnMissingBean` instead, so consumers opt in explicitly?"*

When you see a transitive dependency contributing unexpected beans:
*"Where is this bean coming from? Run with `--debug` or check `/actuator/conditions` to trace it. If a dependency is registering beans you didn't ask for, that dependency is violating your application's boundary."*

Why it matters: Your Spring context is your application's **composition root**. Every bean in it should be there because you — or an auto-configuration you explicitly enabled — put it there. Implicit bean registration from the classpath is ambient authority. It's the DI equivalent of a global variable: anyone can add one, nobody knows who did, and you discover the problem in production.

Registration (Boot 2.x): List auto-configuration classes in `META-INF/spring.factories` under `org.springframework.boot.autoconfigure.EnableAutoConfiguration`
Registration (Boot 3.x): List auto-configuration classes in `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`

Reference: [Spring Boot Docs — Creating Your Own Auto-configuration](https://docs.spring.io/spring-boot/reference/features/developing-auto-configuration.html)

### Bean Classes Should Be POJOs
- Business logic classes should have **no Spring annotations** on them.
- `@Configuration` classes wire POJOs into the context.
- This means your domain and service code is framework-independent and testable in isolation.

When you see `@Service` or `@Component` on a class with business logic:
*"This class contains business logic but is annotated with `@Service`, coupling it to Spring's component model. Would it work to remove the annotation and wire it via an explicit `@Bean` method in a `@Configuration` class?"*

---

## Configuration

### Profiles ≠ Environments
- `SPRING_PROFILES_ACTIVE` is a feature toggle mechanism, not an environment identity.
- Profiles are additive, stackable, and not type-safe. They answer "what features?" not "where am I?"
- Prefer a single authoritative signal (e.g., `APP_ENV=PROD`) resolved via `EnvironmentPostProcessor` to load deterministic config.

When you see profile-conditional beans (`@Profile("prod")`):
*"Profiles are additive — activating 'prod' and 'debug' simultaneously is one typo away. Does this need to be a profile, or is it really an environment-specific behavior that should be driven by a `DeploymentEnvironment` enum?"*

Reference: [Spring Docs — EnvironmentPostProcessor](https://docs.spring.io/spring-boot/api/org/springframework/boot/env/EnvironmentPostProcessor.html)

### Typed Configuration over `@Value`
- Extract related properties into **typed records** (Java 16+) or immutable classes with constructor validation.
- Bind with `Binder.get(env).bind(...)` for explicit control, or `@ConfigurationProperties` for Boot's built-in binding + validation.
- `@Value("${...}")` scatters property access across the codebase with no compile-time safety.

When you see `@Value`:
*"This injects a raw property string with no type safety or validation. Could these related properties be grouped into a typed record bound via `Binder` or `@ConfigurationProperties`? That way misconfiguration fails at startup, not at 2 AM."*

Reference: [Spring Boot Docs — Type-safe Configuration Properties](https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.typesafe-configuration-properties)

### Secrets Are Not Properties
- Distinguish between **wiring config** (needed at context startup) and **runtime secrets** (needed at connection time).
- Define a `SecretProvider` interface. Resolve secrets lazily, not during context boot.
- This separates "where do I find secrets?" (config) from "what is the secret value?" (runtime).

### Interface Segregation for Configuration
- A `DataSource` bean should not have access to email retry settings.
- Provide narrow, typed configuration interfaces to each consumer — only the config it needs.
- This applies ISP (Interface Segregation Principle) to configuration, not just code.

---

## Testing

### The Testing Hierarchy
The cost of a test is **startup time + fragility + signal clarity**. Optimize for all three.

#### 1. Unit Tests — First and Fast
- Instantiate classes with `new`. Pass dependencies via constructor.
- **No Spring context. No annotations. No framework.**
- These tests run in milliseconds and give immediate design feedback.
- If a class is hard to instantiate with `new`, that's the test telling you the design needs work.

```java
// Preferred: test with new
var service = new OrderService(fakeInventory, stubPricing);
service.placeOrder(order);
```

#### 2. Integration Tests — One Context, Load Once
- Boot the full Spring context **once** across all integration tests.
- Avoid `@MockBean` (Boot 2.x) / `@MockitoBean` (Boot 3.x) in integration tests — each unique mock combination forces a new context, destroying the "load once" benefit.
- Use **bean stomping** (override a bean in a test `@Configuration`) to replace external dependencies with fakes.

When you see `@MockBean` in an integration test:
*"Each `@MockBean` combination creates a new Spring context. If multiple tests mock different beans, CI slows to a crawl. Could this external dependency be replaced with a fake bean in a shared test `@Configuration` instead?"*

**Bean stomping caution**: Overriding a bean with a different type can silently violate the **Liskov Substitution Principle**. The fake should honor the contract of the original.

Reference (Boot 2.x): [Spring Boot Docs — @MockBean](https://docs.spring.io/spring-boot/docs/2.7.x/reference/html/features.html#features.testing.spring-boot-applications.mocking-beans)
Reference (Boot 3.x): [Spring Boot Docs — @MockitoBean](https://docs.spring.io/spring-boot/reference/testing/spring-boot-applications.html#testing.spring-boot-applications.mocking-beans)

#### 3. Web Layer Tests — Beneath the Skin
- Use `MockMvc` with `@WebMvcTest` (Boot 2.x & 3.x) to test the HTTP pipeline: serialization, validation, error handling, content negotiation.
- `@WebMvcTest` loads a **slice** — only web layer beans. `@MockitoBean` / `@MockBean` is appropriate here because you're deliberately isolating the web layer from business logic.
- Test what the **client sees**: status codes, response bodies, error shapes. Not what the controller calls internally.

Reference: [Spring Boot Docs — Testing the Web Layer](https://docs.spring.io/spring-boot/reference/testing/spring-boot-applications.html#testing.spring-boot-applications.spring-mvc-tests)

#### 4. Preferred Test Doubles
- **Fakes over mocks** when the dependency has meaningful behavior (e.g., `FakeInventoryClient` with a builder pattern).
- **Stubs** for simple return values. **Mocks** (Mockito) only when verifying interaction is the point of the test.
- Excessive mocking is a design smell — it usually means the class under test has too many collaborators.

#### 5. Database Tests
- **Own your database.** Each test suite controls its own schema and data.
- Use **Testcontainers** for integration tests against a real database engine.
- Never test against a shared development database. Shared databases are nondeterministic.
- Use **Flyway** or **Liquibase** migrations in tests to ensure schema matches production.

Reference: [Testcontainers — Spring Boot](https://java.testcontainers.org/modules/databases/)

#### 6. Tests Are Design Feedback
- Hard to test → hard to maintain. Listen to the friction.
- A class that needs the full Spring context to test is a class with too many implicit dependencies.
- If you're writing `@DirtiesContext`, ask *why* the context is dirty. That's a symptom, not a solution.

---

## Web Layer

### Understand the DispatcherServlet Pipeline
Before writing controllers, understand what Spring does with an HTTP request:

```
Request → Filter Chain → DispatcherServlet → HandlerMapping
  → HandlerAdapter → @Controller method → ReturnValueHandler
  → ViewResolver / MessageConverter → Response
```

When reviewing controller code, ask: *"Which stage of the pipeline is responsible for this behavior?"* Validation, serialization, error handling, and content negotiation are all pipeline concerns — not controller concerns.

Reference: [Spring Docs — DispatcherServlet](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet.html)

### Thin Controllers, Rich Services
- Controllers should **dispatch and translate**. Translate HTTP into a service call, translate the result into HTTP.
- Business logic belongs in framework-free service classes.
- If a controller method is longer than ~10 lines, it's probably doing too much.

### Error Handling
- Use `@ControllerAdvice` / `@RestControllerAdvice` for centralized exception mapping.
- Return consistent error shapes (e.g., RFC 7807 Problem Details).
- Boot 3.x: Use `ProblemDetail` directly. Boot 2.x: Define your own error DTO.
- Don't let stack traces leak to clients.

Reference (Boot 3.x): [Spring Docs — RFC 7807 Error Responses](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-rest-exceptions.html)

---

## Auto-Configuration Diagnostics
When debugging unexpected bean behavior:
- **Boot 2.x & 3.x**: Run with `--debug` flag to print the auto-configuration report at startup.
- **Boot 2.x & 3.x**: Use the `/actuator/conditions` endpoint (requires `spring-boot-actuator`).
- These tools answer: "Why was this bean created?" and "Why was this auto-configuration skipped?"

---

## Migration Guidance (Boot 2.x → 3.x)
When working in a Boot 2.x project, gently identify migration opportunities:
- `javax.*` → `jakarta.*` (the biggest breaking change)
- `spring.factories` auto-config registration → `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`
- `@MockBean` → `@MockitoBean` (Boot 3.4+)
- `WebSecurityConfigurerAdapter` → component-based `SecurityFilterChain` `@Bean`
- Spring Cloud Bootstrap context → `spring.config.import`

Don't push migration in every review. Flag it when the developer is already touching related code — that's the natural seam.

Reference: [Spring Boot 3.0 Migration Guide](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Migration-Guide)

---

## Code Review Checklist (Quick Reference)
When reviewing or generating Spring code, check:

| Concern | Ask |
|---|---|
| Field injection | *"Can this use constructor injection with `final` fields?"* |
| `@ComponentScan` | *"Could this module use explicit `@Configuration` + `@Import`?"* |
| `@Value` | *"Could these properties be a typed record or `@ConfigurationProperties` class?"* |
| `@Profile` | *"Is this an environment identity or a feature toggle?"* |
| `@MockBean` in integration test | *"Will this force a new Spring context? Could a shared fake work?"* |
| Business logic in controller | *"Does this belong in a service class that can be tested with `new`?"* |
| `catch (Exception e)` | *"Can this catch a more specific exception?"* |
| `@Autowired` on field | *"This hides the dependency graph. Constructor injection?"* |
| `@Component` in a shared JAR | *"This silently registers beans in every consumer. Auto-configuration with `@Conditional`?"* |
| Unexpected bean in context | *"Run `--debug` or check `/actuator/conditions`. Which dependency contributed this?"* |
| Shared test database | *"Could this use Testcontainers for isolation?"* |
| Raw `Environment.getProperty()` | *"Could a typed config object provide this value with validation?"* |

---

## Principles Reference

SOLID principles and design fundamentals are in design.instructions.md. The following are Spring-specific applications of those principles:

- **DIP applied**: Constructor injection makes dependency inversion visible. `@Bean` methods are factories (Bloch, Item 1).
- **OCP applied**: `@Import` is additive and deliberate; `@ComponentScan` is ambient and implicit.
- **ISP applied**: Typed configuration objects provide only the config each consumer needs.
- **LSP applied**: Test fakes must honor the contract of the real implementation.
- **SRP applied**: 7 constructor params = too many responsibilities. That's design feedback, not a formatting problem.

Reference: [Fowler — Dependency Injection](https://martinfowler.com/articles/injection.html), [Fowler — Inversion of Control](https://martinfowler.com/bliki/InversionOfControl.html)
