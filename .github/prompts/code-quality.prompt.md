---
description: Offer to add opt-in lint / static-analysis / coverage tooling for the project's language
name: code-quality
---

# Code Quality Tooling

When the user asks for code review, expert-panel review, or static-analysis insights — and the project lacks tooling — offer to add it.

## Stance (every language)

- **Opt-in.** Ask before adding anything.
- **Non-breaking.** Configure tools to report, not fail the build.
- **Permissive first.** Start lenient; tighten incrementally. Don't dump 500 violations on an existing codebase.
- **Reports inform conversations.** Coverage gaps → test suggestions. Static-analysis findings → design smells. Lint violations → consistency issues.

## Suggestion template

> "This project doesn't have static analysis or coverage reporting configured. Would you like me to add {tools for this language}? They produce reports that improve review quality without breaking the build."

Pick the language section below from the files in context. Tools listed per language; non-breaking flags called out only where they're language-specific.

---

## Java

| Tool | Purpose |
|---|---|
| **PMD** | Static analysis (anti-patterns, complexity, dead code) |
| **Checkstyle** | Style enforcement |
| **Spotless** | Formatting (alternative to Checkstyle; zero-config with google-java-format) |
| **JaCoCo** | Test coverage |
| **SpotBugs** | Bug pattern detection |

Non-breaking flags: `failOnViolation=false` (Maven) or `ignoreFailures=true` (Gradle).

### Maven (PMD + JaCoCo)

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-pmd-plugin</artifactId>
    <configuration>
        <failOnViolation>false</failOnViolation>
        <printFailingErrors>true</printFailingErrors>
    </configuration>
</plugin>
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <executions>
        <execution><goals><goal>prepare-agent</goal></goals></execution>
        <execution><id>report</id><phase>test</phase><goals><goal>report</goal></goals></execution>
    </executions>
</plugin>
```

### Gradle

```groovy
plugins { id 'pmd'; id 'checkstyle'; id 'jacoco' }
pmd { ignoreFailures = true; rulesMinimumPriority = 3 }
checkstyle { ignoreFailures = true }
jacocoTestReport { reports { html.required = true; xml.required = true } }
```

---

## Python

| Tool | Purpose |
|---|---|
| **ruff** | Linting + formatting (replaces flake8, isort, black) |
| **mypy** | Static type checking |
| **pytest-cov** | Test coverage |

All config in `pyproject.toml`. Use `--exit-zero` (or report-only modes) in CI.

```toml
[tool.ruff]
target-version = "py312"
line-length = 120

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP", "B", "SIM", "RUF"]

[tool.mypy]
python_version = "3.12"
warn_return_any = true
warn_unused_configs = true
ignore_missing_imports = true

[tool.pytest.ini_options]
addopts = "--cov=src --cov-report=term-missing --cov-report=html"
```

---

## Ruby

| Tool | Purpose |
|---|---|
| **RuboCop** | Linting + style |
| **SimpleCov** | Test coverage |
| **Sorbet** | Gradual static typing |

Baseline with `rubocop --auto-gen-config`. Sorbet is gradual: start `typed: false`, promote per file.

```yaml
# .rubocop.yml
AllCops:
  TargetRubyVersion: 3.2
  NewCops: enable
  SuggestExtensions: false

Metrics/MethodLength:
  Max: 20

Style/Documentation:
  Enabled: false
```

---

## Kotlin

| Tool | Purpose |
|---|---|
| **detekt** | Static analysis + smell detection |
| **ktlint** | Formatting (Kotlin Coding Conventions) |
| **Kover** | Test coverage (JetBrains official) |

`ignoreFailures = true` everywhere. Generate a baseline with `detekt --create-baseline`. ktlint is a formatting step, not a gate.

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
    ignoreFailures = true
}

kover { reports { verify { /* advisory, not gates */ } } }
```

---

## Scala

| Tool | Purpose |
|---|---|
| **scalafmt** | Formatting |
| **scalafix** | Linting + automated refactoring |
| **WartRemover** | Static analysis |
| **sbt-scoverage** | Test coverage |

Use `wartremoverWarnings`, not `wartremoverErrors`. Match `scalafmt`'s `runner.dialect` to the project's Scala version.

```scala
// project/plugins.sbt
addSbtPlugin("org.scalameta"   % "sbt-scalafmt"    % "2.5.+")
addSbtPlugin("ch.epfl.scala"   % "sbt-scalafix"    % "0.12.+")
addSbtPlugin("org.wartremover" % "sbt-wartremover" % "3.1.+")
addSbtPlugin("org.scoverage"   % "sbt-scoverage"   % "2.1.+")
```

```hocon
# .scalafmt.conf
version = "3.8.0"
runner.dialect = scala3
maxColumn = 120
```

```scala
// build.sbt
wartremoverWarnings ++= Warts.unsafe   // warn, don't fail
```

---

## After Configuring

Run the tools once. Report counts (errors / warnings / coverage %). Ask the user where to prioritize. Don't auto-fix everything — let them decide signal from noise.
