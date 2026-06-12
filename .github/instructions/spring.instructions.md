---
applyTo: "**/*.java,**/*.kt,**/*.groovy,**/application*.yml,**/application*.yaml,**/application*.properties,**/bootstrap*.yml,**/bootstrap*.yaml,**/bootstrap*.properties,**/spring.factories,**/*.imports,**/META-INF/spring/**"
---

# Spring

Rules-first guidance. Rationale only where it changes behavior. For deep design discussion, invoke `/expert-panel`.

> Spring is glue. Not a global property oracle.

## Version Detection

- **Boot 3.x / Spring 6.x**: `jakarta.*`, `@MockitoBean`, auto-config registered via `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`.
- **Boot 2.x / Spring 5.x**: `javax.*`, `@MockBean`, auto-config via `META-INF/spring.factories`.
- Detect from `spring-boot-starter-parent` version (Maven) or the `org.springframework.boot` plugin version (Gradle).
- Generate code for the detected version. Don't force 3.x APIs on a 2.x project. Flag migration opportunities only when the dev is already touching related code.

## Bean Wiring

- **Constructor injection only.** Fields `final`. Spring 4.3+ auto-detects a single constructor — no `@Autowired` needed.
- **No field injection.** Rationale: it hides the dependency graph and breaks `new`-instantiable tests.
- **Explicit `@Configuration` + `@Bean` over `@ComponentScan`.** Compose with `@Import`. Rationale: `@ComponentScan` is ambient authority — any `@Component` on the classpath silently joins the context.
- **No `@Service`/`@Component`/`@Repository` on business logic.** Domain code stays framework-free; wire it via `@Bean` methods. POJOs are testable with `new`.
- **Shared library JARs must not register beans via stereotype scanning.** Offer auto-configuration with `@ConditionalOnClass` + `@ConditionalOnProperty` + `@ConditionalOnMissingBean`. Consumers opt in; you can't accidentally inherit beans.
- A class with 7 constructor params is telling you it has too many responsibilities. That's SRP feedback, not a formatting issue.

## Configuration

- **Typed config over `@Value`.** Bind related properties into a record or `@ConfigurationProperties` class. `@Value` scatters property access with no validation; misconfiguration should fail at startup, not at 2 AM.
- **Profiles are feature toggles, not environments.** `SPRING_PROFILES_ACTIVE` is additive and stackable — activating "prod" and "debug" together is one typo away. Prefer a single authoritative signal (e.g., `APP_ENV=PROD`) via `EnvironmentPostProcessor`.
- **Secrets ≠ properties.** Resolve lazily through a `SecretProvider` interface, not at context boot.
- **ISP for configuration.** A `DataSource` bean should not see email retry settings. Give each consumer the narrow typed config it needs.

## Testing

Cost of a test = startup time + fragility + signal clarity. Optimize for all three.

1. **Unit tests first.** Instantiate with `new`. No Spring context, no annotations. If a class can't be `new`'d in a test, that's design feedback.
2. **Integration tests: one context, loaded once.** Avoid `@MockBean` / `@MockitoBean` — each unique mock combination spawns a new context. Use **bean stomping**: override a bean in a test `@Configuration` with a fake that honors the contract (LSP).
3. **Web layer: `@WebMvcTest` + `MockMvc`.** This is a slice — `@MockitoBean` is appropriate here because isolation is the point. Test what the client sees: status codes, response bodies, error shapes.
4. **Test doubles**: fakes when behavior matters, stubs for simple returns, mocks only when verifying interaction is the point. Excessive mocking = too many collaborators.
5. **Database tests**: own the schema; Testcontainers against a real engine; Flyway/Liquibase migrations. Never a shared dev DB.
6. **`@DirtiesContext` is a symptom.** Ask why the context is dirty before reaching for it.

## Web Layer

- **Thin controllers, rich services.** Controllers dispatch and translate HTTP. Logic lives in framework-free services. A controller method >~10 lines is doing too much.
- **Errors via `@RestControllerAdvice`** with consistent shapes (RFC 7807 Problem Details — `ProblemDetail` direct in Boot 3.x, custom DTO in 2.x). Don't leak stack traces.
- Validation, serialization, error handling, content negotiation are pipeline concerns — not controller concerns.

## Diagnostics

- `--debug` flag prints the auto-configuration report at startup.
- `/actuator/conditions` (needs `spring-boot-actuator`) explains why beans were or weren't created.
- Use these when an unexpected bean appears: trace where it came from.

## Boot 2.x → 3.x Migration Triggers

Flag these only when the dev is already in the area:

- `javax.*` → `jakarta.*`
- `META-INF/spring.factories` → `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`
- `@MockBean` → `@MockitoBean` (Boot 3.4+)
- `WebSecurityConfigurerAdapter` → component-based `SecurityFilterChain` bean
- Spring Cloud Bootstrap context → `spring.config.import`

## Review Checklist

| Smell | Ask |
|---|---|
| `@Autowired` field | Constructor injection with `final`? |
| `@ComponentScan` | Could this be explicit `@Configuration` + `@Import`? |
| `@Value` | Typed record or `@ConfigurationProperties`? |
| `@Profile` | Environment identity or feature toggle? |
| `@MockBean` in integration test | New Spring context per combo. Shared fake? |
| Logic in controller | Move to a service that's `new`-testable? |
| `catch (Exception e)` | More specific exception? |
| `@Component` in shared JAR | Auto-configuration with `@Conditional` guards? |
| Mystery bean | `--debug` or `/actuator/conditions` — which dependency? |
| Shared test DB | Testcontainers for isolation? |
| Raw `Environment.getProperty()` | Typed config object with validation? |
