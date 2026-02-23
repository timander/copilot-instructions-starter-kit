---
applyTo: "**/*.scala,**/*.sc,**/build.sbt,**/project/*.scala,**/project/*.sbt"
---

# Scala Guidelines

Scala-specific syntax, idioms, and conventions. Design principles are in design.instructions.md; XP workflow in xp.instructions.md; refactoring in refactoring.instructions.md.

## IDE Preference

Prefer IntelliJ IDEA with Scala plugin actions and refactorings when available:
- Use IntelliJ's built-in refactoring actions (Rename, Extract Method, Inline, etc.)
- Prefer running tests through the IDE test runner
- Use Metals in VS Code as an alternative with BSP support

## Version Detection

Detect Scala version before generating code:
- sbt: `scalaVersion` in `build.sbt`
- Maven: `scala.version` property, `scala-maven-plugin`
- Gradle: `scala-library` dependency version
- Detect **Scala 2 vs Scala 3** — the syntax and idioms differ significantly
- Adapt syntax to detected version; never use features unavailable in the project's Scala version

### Version-Specific Features
| Version | Key Features to Use |
|---------|---------------------|
| 2.12 | Java 8 lambdas, `Either` right-bias, SAM conversion |
| 2.13 | New collections library, `Using` for resource management, `tap`/`pipe`, `LazyList` |
| 3.0-3.2 | New syntax (optional braces, `then`/`do`), enums, union/intersection types, extension methods, `given`/`using` (replaces implicits), opaque types, export clauses |
| 3.3 | Fewer braces, `boundary`/`break`, improved type inference |
| 3.4+ | Named tuples, improved match types, SIP-56 term inference |

### Scala 2 vs Scala 3 Migration
When Scala 2 is detected, generate Scala 2 syntax:
- `implicit` parameters, classes, conversions
- `case class` + `sealed trait` for ADTs
- `import scala.collection.mutable` when needed

When Scala 3 is detected, prefer modern syntax:
- `given`/`using` over `implicit`
- `enum` for ADTs over `sealed trait` + `case class`/`case object`
- Extension methods over implicit classes
- Optional braces (significant indentation) if project uses them
- `export` clauses over inheritance for delegation

## Idiomatic Scala

Write Scala that balances functional purity with pragmatism. Favor clarity over cleverness.

### Functional-First Patterns
- Prefer immutable data: `val` over `var`, immutable collections by default
- Use `case class` for value types; leverage pattern matching and structural equality
- Use `sealed trait`/`enum` + pattern matching for closed type hierarchies
- Use `Option[T]` instead of `null`; use `Either[E, A]` or a dedicated error type for recoverable errors
- Use `for`-comprehensions for chaining `Option`, `Either`, `Future`, or effects
- Prefer higher-order functions (`map`, `flatMap`, `filter`, `fold`) over loops
- Use `match` for exhaustive pattern matching; compiler warns on missing cases with sealed types
- Use partial functions (`collect`, `PartialFunction`) when appropriate

### Type System
- Leverage the type system to make illegal states unrepresentable
- Use type parameters for generic code; avoid `Any` or `AnyRef`
- Use type aliases for complex signatures: `type Result[A] = Either[AppError, A]`
- Use `opaque type` (Scala 3) or `AnyVal` wrapper (Scala 2) for type-safe domain primitives
- Prefer structural types and union types (Scala 3) when they improve clarity

### What to Avoid
- `null` — use `Option` or a typed error
- `var` unless performance-critical and locally scoped
- Mutable collections unless encapsulated within a method
- Implicit conversions (Scala 2) that surprise — prefer explicit conversion methods
- Deep `implicit` chains that are hard to trace; prefer `given`/`using` (Scala 3) with clear scope
- `return` statements — Scala methods return the last expression
- Overusing symbolic operators — prefer named methods for clarity

## Style & Conventions

- Official Scala Style Guide unless project config differs
- `camelCase` for methods, values, variables
- `PascalCase` for classes, traits, objects, type parameters
- Method names should read as verbs or verb phrases: `findById`, `validateInput`
- Prefer named arguments for methods with >2 parameters or boolean flags
- Two-space indentation (Scala standard)
- Infix notation only for well-known operators and DSLs; prefer dot notation for methods

## Error Handling

- Use `Option[T]` for the absence of a value
- Use `Either[Error, Success]` for recoverable errors with context
- Use `Try[T]` for wrapping exceptions from Java interop or I/O
- Throw exceptions only for truly unexpected, unrecoverable failures
- Pattern match on error types; avoid `try/catch` except at boundaries
- For effect systems (Cats Effect, ZIO), use their typed error channels

## Testing

Detect test framework from build configuration before generating test code:

| Dependency | Framework | Use |
|------------|-----------|-----|
| `scalatest` | ScalaTest | Multiple styles: `FunSuite`, `FlatSpec`, `WordSpec`, `AnyFunSuite`; `Matchers` |
| `munit` | MUnit | `FunSuite`, `assertEquals`, lightweight |
| `specs2` | Specs2 | `Specification`, acceptance-style |
| `scalacheck` | ScalaCheck | Property-based testing with `Gen`, `Prop`, `forAll` |
| `scalamock` | ScalaMock | `mock[T]`, expectations-based mocking |

- Match the framework and style already in use in the project
- ScalaTest: `AnyFunSuite` or `AnyFlatSpec` with `Matchers` is the most common style
- Use ScalaCheck generators for property-based tests alongside unit tests
- Descriptive test names that document behavior
- One concept per test; Arrange-Act-Assert structure

## Build Tool: sbt

When `build.sbt` is present:
- Use `sbt` conventions for project layout (`src/main/scala`, `src/test/scala`)
- Multi-project builds use `lazy val` project references
- Use `%%` for Scala-version-specific dependencies, `%` for Java
- Use `ThisBuild / scalaVersion` for shared settings

## Code Quality Tooling (Optional)

These tools are **not required** but can significantly enhance code review and expert panel analysis by producing actionable reports. Adding them is an opt-in decision — when prompted, offer to configure them.

When a user asks for code review, expert panel review, or static analysis insights, suggest:
*"This project doesn't have static analysis or coverage reporting configured. Would you like me to add scalafmt, scalafix, WartRemover, and sbt-scoverage? They produce reports that improve review quality without breaking the build."*

### Recommended Tools
| Tool | Purpose | Config |
|------|---------|--------|
| **scalafmt** | Code formatting | `.scalafmt.conf` |
| **scalafix** | Linting + automated refactoring | `.scalafix.conf`, sbt plugin |
| **WartRemover** | Static analysis (catches common pitfalls) | sbt plugin config |
| **sbt-scoverage** | Test coverage reporting | sbt plugin `org.scoverage` |

### Configuration Principles
- **Non-breaking**: tools should report, not fail the build; use `wartremoverWarnings` (not `wartremoverErrors`)
- Reports feed into code review: coverage gaps inform test suggestions, WartRemover findings surface code smells, scalafix flags deprecated patterns
- Start permissive, tighten incrementally — disable noisy rules initially
- scalafmt: use `runner.dialect` matching the project's Scala version

### Example Configurations

```scala
// project/plugins.sbt
addSbtPlugin("org.scalameta"  % "sbt-scalafmt"   % "2.5.+")
addSbtPlugin("ch.epfl.scala"  % "sbt-scalafix"   % "0.12.+")
addSbtPlugin("org.wartremover" % "sbt-wartremover" % "3.1.+")
addSbtPlugin("org.scoverage"  % "sbt-scoverage"  % "2.1.+")
```

```hocon
# .scalafmt.conf
version = "3.8.0"
runner.dialect = scala3  # or scala213, match project
maxColumn = 120
```

```scala
// build.sbt — WartRemover as warnings only
wartremoverWarnings ++= Warts.unsafe  // warn, don't fail
```

Reference: Odersky et al.'s *Programming in Scala*; official Scala Documentation
