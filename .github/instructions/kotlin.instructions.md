---
applyTo: "**/*.kt,**/*.kts"
---

# Kotlin Guidelines

Kotlin-specific syntax, idioms, and conventions. Design principles are in design.instructions.md; XP workflow in xp.instructions.md; refactoring in refactoring.instructions.md. For Spring-specific Kotlin, see also spring.instructions.md.

## IDE Preference

Prefer IntelliJ IDEA actions and refactorings over command-line tools when available:
- Use IntelliJ's built-in refactoring actions (Rename, Extract Function, Inline, etc.)
- Prefer running tests through the IDE test runner
- Use IntelliJ's Kotlin inspections and quick-fix suggestions

## Version Detection

Detect Kotlin version before generating code:
- Gradle: `kotlin("jvm")` plugin version, `kotlin-stdlib` dependency version in `build.gradle.kts`
- Maven: `<kotlin.version>` property in `pom.xml`
- Also detect the **JVM target**: `jvmTarget` in `build.gradle.kts` or `<jvmTarget>` in Maven `kotlin-maven-plugin`
- Adapt syntax to detected version; never use features unavailable in the project's Kotlin version

### Version-Specific Features
| Version | Key Features to Use |
|---------|---------------------|
| 1.4 | `SAM` conversions for Kotlin interfaces, trailing comma support |
| 1.5 | `value class` (inline classes), sealed interfaces, `JvmRecord` |
| 1.6 | Exhaustive `when` for sealed/enum, suspend conversions |
| 1.7 | `opt-in` requirements stable, builder inference improvements |
| 1.8 | `@JvmDefault` changes, improved type inference |
| 1.9 | K2 compiler (beta), `..< ` open-ended ranges, entries for enums |
| 2.0+ | K2 compiler stable, `@SubclassOptInRequired`, context parameters (experimental) |

## Idiomatic Kotlin

Write Kotlin that leverages the language's strengths — conciseness, null safety, and expressiveness — without sacrificing clarity.

### Kotlin-Specific Patterns
- Use `data class` for value types; prefer immutable `val` properties
- Use `sealed class`/`sealed interface` + `when` for modeling closed hierarchies exhaustively
- Use `object` for singletons and companion factories
- Use extension functions to add behavior without inheritance; keep them discoverable
- Use `require()`, `check()`, `error()` for preconditions
- Use `scope functions` intentionally: `let` for null checks, `apply` for configuration, `also` for side effects, `run` for transformations, `with` for grouped calls
- Use property delegation (`by lazy`, `by Delegates.observable`) for computed/cached properties
- Use `sequence { }` for lazy iteration over large datasets
- Destructuring declarations for data classes and pairs: `val (name, age) = person`

### What to Avoid
- `!!` (non-null assertion) — use safe calls, `Elvis`, or restructure to eliminate null
- Platform types going unchecked — annotate Java interop boundaries
- Overusing scope functions — if nesting exceeds 2 levels, extract a named function
- `var` when `val` works; mutable collections when read-only suffice
- Java patterns transplanted without Kotlin adaptation (e.g., builder pattern when named/default args suffice, `static` utility classes when top-level functions work)

## Null Safety

Kotlin's type system distinguishes nullable from non-null. Use it fully:
- Prefer non-null types; make nullability explicit only when semantically meaningful
- Use safe call (`?.`) and Elvis (`?:`) operators
- Avoid `!!` — if you must use it, leave a `// TODO` explaining the constraint
- Use `?.let { }` for nullable transformations
- Narrow nullability at API boundaries; internal code should be non-null

## Style & Conventions

- Kotlin Coding Conventions (official) unless project config differs
- `camelCase` for functions, properties, local variables
- `PascalCase` for classes, interfaces, sealed classes, type aliases
- `SCREAMING_SNAKE_CASE` for `const val` compile-time constants
- No Hungarian notation
- Prefer named arguments for functions with >2 parameters or boolean flags
- Prefer expression bodies for short functions: `fun isValid() = value > 0`
- Trailing commas in multi-line argument lists and collections
- Use `typealias` for complex function types or generic signatures

## Error Handling

- Use `Result<T>` or sealed class hierarchies for expected failures
- Throw specific exceptions with context for unexpected failures
- Use `runCatching { }` judiciously — don't swallow errors silently
- Prefer `require()` for argument validation, `check()` for state validation
- Use `use { }` (Closeable.use) for resource management

## Testing

Detect test framework from build configuration before generating test code:

| Dependency | Framework | Use |
|------------|-----------|-----|
| `junit-jupiter` | JUnit 5 | `@Test`, `@BeforeEach`, `@DisplayName`, assertions |
| `kotest` | Kotest | Multiple styles: `FunSpec`, `BehaviorSpec`, `StringSpec`; `shouldBe` matchers |
| `mockk` | MockK | `mockk<T>()`, `every { }`, `verify { }` — idiomatic Kotlin mocking |
| `kotlin-test` | kotlin.test | `@Test`, `assertEquals`, multiplatform support |

- Match the framework and style already in use in the project
- Prefer MockK over Mockito for Kotlin — it handles coroutines, extension functions, and final classes natively
- Descriptive test names: use backtick names `` `should return empty list when input is null` ``
- One concept per test; Arrange-Act-Assert structure

## Java Interop

- Use `@JvmStatic`, `@JvmField`, `@JvmOverloads` for clean Java-facing APIs
- Annotate nullable parameters with `@Nullable` / `@NotNull` when exposing to Java
- Prefer Kotlin collections internally; convert at Java boundaries when needed

## Code Quality Tooling (Optional)

These tools are **not required** but can significantly enhance code review and expert panel analysis by producing actionable reports. Adding them is an opt-in decision — when prompted, offer to configure them.

When a user asks for code review, expert panel review, or static analysis insights, suggest:
*"This project doesn't have static analysis or coverage reporting configured. Would you like me to add detekt, ktlint, and Kover? They produce reports that improve review quality without breaking the build."*

### Recommended Tools
| Tool | Purpose | Config |
|------|---------|--------|
| **detekt** | Static analysis + code smell detection | `detekt.yml` or Gradle plugin config |
| **ktlint** | Code formatting (Kotlin Coding Conventions) | Gradle plugin or CLI |
| **Kover** | Test coverage reporting (JetBrains official) | Gradle plugin `org.jetbrains.kotlinx.kover` |

### Configuration Principles
- **Non-breaking**: tools should report, not fail the build; use `ignoreFailures = true` in Gradle
- Reports feed into code review: coverage gaps inform test suggestions, detekt findings surface design issues
- Start permissive, tighten incrementally — generate a baseline with `detekt --create-baseline`
- ktlint enforces official Kotlin conventions; integrate as a formatting step, not a gate

### Example Gradle Configuration

```kotlin
// build.gradle.kts
plugins {
    id("io.gitlab.arturbosch.detekt") version "1.23.+"
    id("org.jlleitschuh.gradle.ktlint") version "12.+"
    id("org.jetbrains.kotlinx.kover") version "0.8.+"
}

detekt {
    buildUponDefaultConfig = true
    config.setFrom("$projectDir/detekt.yml")
    ignoreFailures = true  // report only, don't break the build
}

kover {
    reports {
        verify { /* thresholds are advisory, not gates */ }
    }
}
```

Reference: Jemerov & Isakova's *Kotlin in Action*; official Kotlin Coding Conventions
