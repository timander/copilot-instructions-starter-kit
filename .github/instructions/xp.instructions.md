---
applyTo: "**/*test*.{java,kt,ts,tsx,js,jsx,py,rb,go,rs,cs,scala,groovy},**/*spec*.{java,kt,ts,tsx,js,jsx,py,rb,go,rs,cs,scala,groovy},**/__tests__/**,**/test/**,**/tests/**,**/spec/**"
---

# Testing Discipline

Auto-loaded only on test files. The full red-green-refactor protocol lives in `/tdd`.

## Principles

- Test **behavior**, not implementation. A test that breaks during a refactor was testing the wrong thing.
- **One concept per test.** Multiple asserts on the same object is fine; multiple concepts in one test is not.
- **Descriptive names** that document behavior. `shouldReturnEmptyList_whenInputIsNull` over `test1`.
- **Arrange–Act–Assert** structure, with blank lines between phases when it aids reading.
- **Edge cases**: null, empty, boundary, exhausted iterator, max-size, negative.
- **Fast.** Mock or fake external dependencies. A slow test suite gets skipped.
- **Tests are documentation.** A reader should understand the unit by reading its tests.

## Discipline

- No production code without a failing test.
- If you can't write a test, the design needs to change — that's the feedback. Don't power through it.
- If a step feels big, break it smaller.
- Tests are first-class design artifacts, not afterthoughts. They get refactored too.
