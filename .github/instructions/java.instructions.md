---
applyTo: "**/*.java,**/pom.xml,**/build.gradle,**/build.gradle.kts"
---

# Java Guidelines

## Version Detection
- Check `pom.xml` for `<maven.compiler.source>`, `<maven.compiler.release>`, or `<java.version>` property
- Check `build.gradle` for `sourceCompatibility` or `toolchain { languageVersion }`
- Use features appropriate for the detected version (e.g., records for 16+, pattern matching for 21+)

## Modern Java (17+)
- Prefer records for immutable data carriers
- Use sealed classes/interfaces when inheritance should be constrained
- Use pattern matching in `instanceof` and switch expressions
- Prefer `switch` expressions over `switch` statements when returning a value
- Use text blocks for multi-line strings

## Style
- Follow Google Java Style Guide unless project `.editorconfig` or Checkstyle says otherwise
- One class per file (except for private nested classes)
- Imports: no wildcards, static imports for test assertions and common utilities only
- Prefer `final` for local variables when feasible (especially in lambdas)

## Null Safety
- Use `Optional` for return types that may be absent—never return `null` from a method that could return `Optional`
- Annotate parameters and fields with `@Nullable` or `@NonNull` if project uses nullability annotations
- Fail fast on null arguments with `Objects.requireNonNull()` at method entry

## Collections & Streams
- Prefer immutable collections (`List.of()`, `Set.of()`, `Map.of()`) for constants and returns
- Use streams for transformation pipelines; prefer loops for side-effects or early exit
- Don't nest streams more than 2 levels deep—extract methods

## Error Handling
- Throw specific exceptions (not `Exception` or `RuntimeException` directly)
- Include context in exception messages (what operation, what input)
- Catch specific exceptions; avoid `catch (Exception e)` unless truly necessary

## Testing
- JUnit 5 preferred (`@Test`, `@BeforeEach`, etc.)
- Use AssertJ for fluent assertions when available
- Test names describe behavior: `shouldReturnEmptyWhenInputIsNull()`
- One assertion concept per test (multiple asserts on same object is fine)

## Dependency Injection
- Prefer constructor injection over field injection
- Keep constructors simple—don't do work in constructors
- Use `@Autowired` on constructor only when multiple constructors exist (Spring)

## Naming
- `camelCase` for methods and variables
- `PascalCase` for classes and interfaces
- `SCREAMING_SNAKE_CASE` for `static final` constants
- No Hungarian notation, no type prefixes (no `IService`, `AbstractBase`)
