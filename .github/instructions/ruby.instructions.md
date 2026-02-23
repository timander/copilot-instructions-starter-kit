---
applyTo: "**/*.rb,**/Gemfile,**/*.gemspec,**/Rakefile,**/.rubocop.yml"
---

# Ruby Guidelines

Ruby-specific syntax, idioms, and conventions. Design principles are in design.instructions.md; XP workflow in xp.instructions.md; refactoring in refactoring.instructions.md.

## Version Detection

Detect Ruby version before generating code:
- `.ruby-version` file
- `Gemfile`: `ruby '3.x.x'` declaration
- `.tool-versions` (asdf)
- `*.gemspec`: `required_ruby_version`
- Adapt syntax to detected version; never use features unavailable in the project's Ruby version

### Version-Specific Features
| Version | Key Features to Use |
|---------|---------------------|
| 2.7 | Pattern matching (experimental), numbered block params (`_1`), `Enumerator::Lazy` |
| 3.0 | Ractor (actor concurrency), `Hash#except`, one-line pattern matching (`in`), end-of-positional-args (`...`) |
| 3.1 | Hash literal shorthand (`x:` for `x: x`), `debug` gem built-in, `error_highlight` |
| 3.2 | `Data.define` (immutable value objects), anonymous rest/keyword rest forwarding |
| 3.3 | `it` as default block parameter (experimental), `Range#overlap?` |
| 3.4+ | `Modular GC`, improved Prism parser |

## Package Manager: Bundler

Use **Bundler** as the package/dependency manager:
- `bundle init` to start a new project
- `bundle add <gem>` to add dependencies
- `bundle install` to install from `Gemfile.lock`
- `bundle exec <command>` to run within the bundle context
- Always commit `Gemfile.lock` for applications; omit for libraries
- Respect existing `Gemfile` conventions if present

## Idiomatic Ruby

Write Ruby that embraces the language's expressiveness while maintaining clarity.

### Ruby Principles
- **Duck typing**: Rely on behavior, not class identity; use `respond_to?` sparingly
- **Convention over configuration**: Follow community naming and structure conventions
- **Principle of least surprise (POLS)**: Methods should behave as their name suggests
- **Everything is an object**: Leverage this — methods, blocks, procs, classes

### Ruby Patterns
- Use blocks and `yield` for control flow inversion; `Enumerable` for collection processing
- Use `Symbol#to_proc` for simple mappings: `names.map(&:upcase)`
- Use `Struct` or `Data.define` (3.2+) for value objects; avoid bare hashes for structured data
- Use `freeze` for constants: `DEFAULTS = { timeout: 30 }.freeze`
- Use `Comparable` mixin with `<=>` for sortable objects
- Use `Enumerable` mixin for custom collections
- Prefer `each_with_object` or `transform_values` over manual accumulation
- Use `begin/rescue/ensure` for exception handling; `retry` when appropriate
- Prefer keyword arguments over positional for methods with >2 parameters

### What to Avoid
- Monkey-patching core classes (unless you're building a DSL with clear boundaries)
- `for` loops — use `each`, `map`, `select`, etc.
- `and`/`or` for control flow — use `&&`/`||`
- `class_eval` / `instance_eval` unless metaprogramming is intentional and documented
- Deep inheritance hierarchies — prefer composition and modules
- String-keyed hashes when symbols are appropriate

## Style & Conventions

- Community Ruby Style Guide unless project `.rubocop.yml` differs
- `snake_case` for methods, variables, file names
- `PascalCase` for classes and modules
- `SCREAMING_SNAKE_CASE` for constants
- Predicate methods end in `?`: `empty?`, `valid?`
- Dangerous methods end in `!`: `save!`, `sort!`
- Two-space indentation
- Trailing commas in multi-line arrays/hashes
- Prefer single quotes for strings without interpolation

## Error Handling

- Raise specific errors with context: `raise ArgumentError, "expected positive, got #{n}"`
- Rescue specific exceptions; avoid bare `rescue` (catches `StandardError`)
- Use custom error classes inheriting from `StandardError` for domain errors
- Use `ensure` for cleanup; prefer `begin/rescue` blocks at method level
- Fail fast with guard clauses at method entry

## Testing

Detect test framework from `Gemfile` before generating test code:

| Dependency | Framework | Use |
|------------|-----------|-----|
| `rspec` | RSpec | `describe`, `context`, `it`, `expect()` |
| `minitest` | Minitest | `class ... < Minitest::Test`, `assert_*` or Minitest::Spec |
| `capybara` | Capybara | System/integration tests with `visit`, `fill_in`, `click_on` |
| `factory_bot` | FactoryBot | Test data with `build`, `create` |

- Match the framework and style already in use in the project
- RSpec: use `let` for lazy setup, `before` blocks for shared context, `subject` for the object under test
- Descriptive test names that document behavior
- One concept per test; Arrange-Act-Assert structure
- Mock external dependencies; prefer dependency injection over `allow`/`expect` stubs when possible

## Code Quality Tooling (Optional)

These tools are **not required** but can significantly enhance code review and expert panel analysis by producing actionable reports. Adding them is an opt-in decision — when prompted, offer to configure them.

When a user asks for code review, expert panel review, or static analysis insights, suggest:
*"This project doesn't have static analysis or coverage reporting configured. Would you like me to add RuboCop, SimpleCov, and Sorbet/steep? They produce reports that improve review quality without breaking the build."*

### Recommended Tools
| Tool | Purpose | Config |
|------|---------|--------|
| **RuboCop** | Linting + style enforcement | `.rubocop.yml` |
| **SimpleCov** | Test coverage reporting | `spec/spec_helper.rb` or `test/test_helper.rb` |
| **Sorbet** | Gradual static typing | `sorbet/config`, `typed:` sigils |

### Configuration Principles
- **Non-breaking**: tools should warn, not fail the build; start with `--auto-gen-config` for RuboCop to baseline
- Reports feed into code review: coverage gaps inform test suggestions, type errors surface design issues, lint violations flag code smells
- Start permissive, tighten incrementally — don't dump hundreds of offenses on an existing codebase
- Sorbet is gradual: start with `typed: false`, promote files as types are added

### Example .rubocop.yml Starter

```yaml
AllCops:
  TargetRubyVersion: 3.2  # match project's Ruby version
  NewCops: enable
  SuggestExtensions: false

Metrics/MethodLength:
  Max: 20

Style/Documentation:
  Enabled: false  # code should be self-documenting
```

Reference: Metz's *Practical Object-Oriented Design*; Olsen's *Eloquent Ruby*
