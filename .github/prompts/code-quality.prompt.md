---
description: Offer to add opt-in lint / static-analysis / coverage tooling for the project's language
name: code-quality
---

# Code Quality Tooling

When the user asks for code review, expert-panel review, or static-analysis insights — and the project doesn't have tooling configured — offer to add it.

## Shared Stance (every language)

- **Not required.** Adding tooling is an opt-in decision. Ask first.
- **Non-breaking.** Configure tools to report, not fail the build. Reports feed into review; they are not gates.
- **Start permissive, tighten incrementally.** Dropping hundreds of violations on an existing codebase is hostile.
- **Reports inform conversations.** Coverage gaps → test suggestions. Static-analysis findings → design smells. Lint violations → consistency issues.

## Detect the Language

From the files in context (`pom.xml`, `pyproject.toml`, `Gemfile`, `build.gradle.kts`, `build.sbt`, etc.), pick the section below. Skip the others.

---

## Java

Suggest: *"This project doesn't have static analysis or coverage reporting configured. Would you like me to add PMD, Checkstyle (or Spotless), and JaCoCo? They produce reports that improve review quality without breaking the build."*

### Recommended Tools

| Tool | Purpose | Config |
|------|---------|--------|
| **PMD** | Static analysis (anti-patterns, complexity, dead code) | Maven/Gradle plugin + ruleset XML |
| **Checkstyle** | Style enforcement | Maven/Gradle plugin + `checkstyle.xml` |
| **Spotless** | Formatting (alternative to Checkstyle for style) | Gradle/Maven plugin, zero-config with google-java-format |
| **JaCoCo** | Test coverage reporting | Maven/Gradle plugin |
| **SpotBugs** | Bug pattern detection (successor to FindBugs) | Maven/Gradle plugin |

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
plugins {
    id 'pmd'
    id 'checkstyle'
    id 'jacoco'
}

pmd { ignoreFailures = true; rulesMinimumPriority = 3 }
checkstyle { ignoreFailures = true }
jacocoTestReport { reports { html.required = true; xml.required = true } }
```

---

## Python

Suggest: *"This project doesn't have static analysis or coverage reporting configured. Would you like me to add ruff, mypy, and pytest-cov? They produce reports that improve review quality without breaking the build."*

### Recommended Tools

| Tool | Purpose | Config |
|------|---------|--------|
| **ruff** | Linting + formatting (replaces flake8, isort, black) | `[tool.ruff]` in `pyproject.toml` |
| **mypy** | Static type checking | `[tool.mypy]` in `pyproject.toml` |
| **pytest-cov** | Test coverage reporting | `--cov` flag or `[tool.pytest.ini_options]` |

All config goes in `pyproject.toml`. Use `--exit-zero` (or report-only modes) in CI to keep tools warning, not gating.

### pyproject.toml

```toml
[tool.ruff]
target-version = "py312"   # match the project's Python version
line-length = 120

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP", "B", "SIM", "RUF"]

[tool.mypy]
python_version = "3.12"
warn_return_any = true
warn_unused_configs = true
ignore_missing_imports = true   # start permissive

[tool.pytest.ini_options]
addopts = "--cov=src --cov-report=term-missing --cov-report=html"
```

---

## Ruby

Suggest: *"This project doesn't have static analysis or coverage reporting configured. Would you like me to add RuboCop, SimpleCov, and Sorbet/steep? They produce reports that improve review quality without breaking the build."*

### Recommended Tools

| Tool | Purpose | Config |
|------|---------|--------|
| **RuboCop** | Linting + style enforcement | `.rubocop.yml` |
| **SimpleCov** | Test coverage reporting | `spec/spec_helper.rb` or `test/test_helper.rb` |
| **Sorbet** | Gradual static typing | `sorbet/config`, `typed:` sigils |

Start with `rubocop --auto-gen-config` to baseline existing violations. Sorbet is gradual: start with `typed: false`, promote files as types land.

### .rubocop.yml starter

```yaml
AllCops:
  TargetRubyVersion: 3.2     # match the project's Ruby version
  NewCops: enable
  SuggestExtensions: false

Metrics/MethodLength:
  Max: 20

Style/Documentation:
  Enabled: false             # code should be self-documenting
```

---

## Kotlin

Suggest: *"This project doesn't have static analysis or coverage reporting configured. Would you like me to add detekt, ktlint, and Kover? They produce reports that improve review quality without breaking the build."*

### Recommended Tools

| Tool | Purpose | Config |
|------|---------|--------|
| **detekt** | Static analysis + code smell detection | `detekt.yml` or Gradle plugin config |
| **ktlint** | Code formatting (Kotlin Coding Conventions) | Gradle plugin or CLI |
| **Kover** | Test coverage reporting (JetBrains official) | Gradle plugin `org.jetbrains.kotlinx.kover` |

`ignoreFailures = true` everywhere. Generate a baseline with `detekt --create-baseline` so existing violations don't drown the signal. ktlint is a formatting step, not a gate.

### build.gradle.kts

```kotlin
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

kover {
    reports { verify { /* thresholds are advisory, not gates */ } }
}
```

---

## Scala

Suggest: *"This project doesn't have static analysis or coverage reporting configured. Would you like me to add scalafmt, scalafix, WartRemover, and sbt-scoverage? They produce reports that improve review quality without breaking the build."*

### Recommended Tools

| Tool | Purpose | Config |
|------|---------|--------|
| **scalafmt** | Code formatting | `.scalafmt.conf` |
| **scalafix** | Linting + automated refactoring | `.scalafix.conf`, sbt plugin |
| **WartRemover** | Static analysis (catches common pitfalls) | sbt plugin config |
| **sbt-scoverage** | Test coverage reporting | sbt plugin `org.scoverage` |

Use `wartremoverWarnings` not `wartremoverErrors`. Match `scalafmt`'s `runner.dialect` to the project's Scala version.

### project/plugins.sbt

```scala
addSbtPlugin("org.scalameta"   % "sbt-scalafmt"    % "2.5.+")
addSbtPlugin("ch.epfl.scala"   % "sbt-scalafix"    % "0.12.+")
addSbtPlugin("org.wartremover" % "sbt-wartremover" % "3.1.+")
addSbtPlugin("org.scoverage"   % "sbt-scoverage"   % "2.1.+")
```

### .scalafmt.conf

```hocon
version = "3.8.0"
runner.dialect = scala3       # or scala213, match the project
maxColumn = 120
```

### build.sbt

```scala
wartremoverWarnings ++= Warts.unsafe   // warn, don't fail
```

---

## After Configuring

Whichever language: run the tools once, surface the report counts (errors / warnings / coverage %), and ask the user where to prioritize. Don't auto-fix everything — let the user decide what's signal and what's noise.
