---
applyTo: "**/*.java,**/pom.xml,**/build.gradle,**/build.gradle.kts"
---

# Java Guidelines

Java-specific syntax, idioms, and conventions. Design principles are in design.instructions.md; XP workflow in xp.instructions.md; refactoring in refactoring.instructions.md.

## IDE Preference

Prefer IntelliJ IDEA actions and refactorings over command-line tools when available:
- Use IntelliJ's built-in refactoring actions (Rename, Extract Method, Inline, etc.)
- Prefer running tests through the IDE test runner
- Use IntelliJ's code inspection and quick-fix suggestions
- Prefer IDE-integrated build/run over raw `mvn` or `gradle` CLI when possible

## Version Detection

Detect Java version before generating code:
- Maven: `<maven.compiler.source>`, `<maven.compiler.release>`, `<java.version>` in pom.xml
- Gradle: `sourceCompatibility`, `toolchain { languageVersion }` in build.gradle(.kts)
- Adapt syntax to detected version; never use features unavailable in project's Java version

### Version-Specific Features
| Version | Key Features to Use |
|---------|---------------------|
| 8 | Lambdas, streams, `Optional`, method references, `java.time` |
| 9-10 | `var` (10+), `List.of()`, `Set.of()`, `Map.of()` (9+) |
| 11 | `var` in lambdas, `String::isBlank`, `Files::readString` |
| 14-16 | Records (16+), `switch` expressions (14+), text blocks (15+) |
| 17+ | Sealed classes, pattern matching in `instanceof` |
| 21+ | Pattern matching in `switch`, record patterns, virtual threads |

## Effective Java (Bloch)

- Favor static factory methods over constructors
- Use builders for objects with many parameters
- Enforce immutability where possible
- Favor composition over inheritance
- Design for inheritance or prohibit it (`final` class)
- Prefer interfaces to abstract classes
- Use `Optional` instead of returning `null`
- Minimize mutability
- Favor `List.of()`, `Set.of()` for immutable collections
- Avoid raw types; use generics properly
- Use enums instead of `int` constants
- Use `@Override` consistently

## Java Testing

Detect test framework versions from `pom.xml` or `build.gradle` before generating test code.

### JUnit Version Detection
| Dependency | Version | Use |
|------------|---------|-----|
| `junit:junit` 4.x | JUnit 4 | `@Test` from `org.junit`, `@Before`, `@After`, `@RunWith`, `Assert.assertEquals()` |
| `org.junit.jupiter:junit-jupiter` 5.x | JUnit 5 | `@Test` from `org.junit.jupiter.api`, `@BeforeEach`, `@AfterEach`, `@DisplayName`, `@ExtendWith` |

- Never mix JUnit 4 and 5 annotations in the same test class
- If both are on the classpath (migration), match the style of the file being edited

### Mockito Version Detection
| Dependency | Version | Use |
|------------|---------|-----|
| `org.mockito:mockito-core` 2.x-4.x | Mockito 2-4 | `@Mock`, `@InjectMocks`, `when().thenReturn()` |
| JUnit 4 + Mockito | | `@RunWith(MockitoJUnitRunner.class)` |
| JUnit 5 + Mockito | | `@ExtendWith(MockitoExtension.class)` |
| Mockito 5+ | Mockito 5 | Stricter stubbing by default; same API |

### Assertions
- Use AssertJ (`assertThat()`) for fluent assertions when available on classpath
- Fall back to framework assertions: JUnit 5 `Assertions`, JUnit 4 `Assert`
- Use static imports for all assertion methods
- Descriptive test names: `shouldReturnEmptyList_whenInputIsNull()`

## Style & Conventions

- Google Java Style Guide unless project config differs
- No wildcard imports (`import java.util.*` forbidden)
- Use static imports for: assertions, matchers, common utilities (`Objects.requireNonNull`)
- One public class per file
- `final` on local variables when feasible
- Naming: `camelCase` methods/variables, `PascalCase` classes, `SCREAMING_SNAKE_CASE` constants
- No Hungarian notation, no `I` prefix on interfaces

## Null Safety

- Return `Optional<T>` for values that may be absent
- Never return `null` when return type is `Optional`
- Validate inputs: `Objects.requireNonNull(param, "param must not be null")`
- Use `@Nullable`/`@NonNull` annotations if project has them
- Fail fast at method entry, not deep in logic

## Error Handling

- Throw specific exceptions with context: what failed, what input caused it
- Catch specific exceptions; avoid `catch (Exception e)`
- Consider checked vs unchecked appropriately for the domain
- Use custom exceptions for domain-specific errors

## Collections & Streams

- Immutable collections for returns and constants: `List.of()`, `Set.of()` (Java 9+)
- Streams for transformations; loops for side-effects or early exit
- Extract methods when stream pipelines exceed 3 operations
- Don't nest streams more than 2 levels

## Dependency Injection

- Constructor injection always; field injection never
- Keep constructors simple: assign fields only, no logic
- Single constructor preferred; `@Autowired` optional in Spring

## Lombok

If Lombok is in `pom.xml` or `build.gradle`:
- Use `@RequiredArgsConstructor` for constructor injection
- Use `@Getter`, `@Builder`, `@Value` for immutable data classes
- Prefer `@Slf4j` over manual logger declaration
- Do not use `@Data` on entities; use explicit annotations
- Acknowledge Lombok when generating code or tests

## Linting & Static Analysis

Respect project's configured tools:
- **Checkstyle**: Follow project's rules; don't generate violations
- **Spotless/google-java-format**: Formatting will be auto-applied
- **PMD**: Avoid flagged anti-patterns (God class, Law of Demeter violations, empty catch blocks)
- **SpotBugs/FindBugs**: No null-pointer issues, resource leaks, or concurrency bugs
- **JaCoCo**: Tests should cover new code; explain coverage gaps

Always ensure generated code compiles and passes existing checks.

Reference: Bloch's *Effective Java*, Langr's *Pragmatic Unit Testing in Java with JUnit*
