---
applyTo: "**/*.ts,**/*.tsx"
---

# TypeScript Guidelines

TypeScript-specific syntax, idioms, and conventions. Design principles are in design.instructions.md; XP workflow in xp.instructions.md; refactoring in refactoring.instructions.md.

## Strict Mode

- All TypeScript must pass `strict: true`
- Never use `any`; prefer `unknown` when type is truly unknown
- If `any` is unavoidable, use `// eslint-disable-next-line` with a justification — not a prose comment

## Types

- Define explicit return types for exported functions
- Use discriminated unions for state that can be one of several shapes
- Prefer interfaces for object shapes; types for unions/intersections
- Use `readonly` for properties that should not be reassigned
- Prefer `as const` for literal type narrowing
- Avoid type assertions (`as`) — narrow with type guards instead

## Null Safety

- Enable `strictNullChecks` (included in `strict: true`)
- Use optional chaining (`?.`) and nullish coalescing (`??`) over manual null checks
- Prefer returning `undefined` over `null` unless interfacing with an API that uses `null`
- Never use `!` (non-null assertion) without strong justification

## Style & Conventions

- `const` by default, `let` when reassignment is needed, never `var`
- Prefer `async/await` over raw promises or callbacks
- No dangling promises — always `await` or explicitly handle
- No wildcard imports; use named imports
- Prefer named exports over default exports for discoverability
- Naming: `camelCase` for variables/functions, `PascalCase` for types/interfaces/classes/components, `SCREAMING_SNAKE_CASE` for true constants

## Functional Patterns

- Prefer immutable data: spread/`Object.freeze` over mutation
- Prefer pure functions: same input → same output, no side effects
- Use `map`, `filter`, `reduce` for transformations; `for...of` for side effects or early exit
- Extract small, named functions over inline arrow function chains

## Error Handling

- Throw `Error` subclasses, not strings
- Catch specific errors when you can handle them; let others propagate
- Include context in error messages: what operation failed, with what inputs
- Use custom error classes for domain-specific errors

## Testing

Detect test framework from `package.json` before generating test code:

| Dependency | Framework | Use |
|------------|-----------|-----|
| `vitest` | Vitest | `describe`, `it`, `expect` from `vitest` |
| `jest` | Jest | `describe`, `it`, `expect` (globals or from `@jest/globals`) |
| `@testing-library/*` | Testing Library | `render`, `screen`, `userEvent` for component tests |
| `playwright` | Playwright | E2E tests with `page`, `expect` |

- Match the framework and style already in use in the project
- Use static imports for test utilities

## Linting & Formatting

Respect project's configured tools:
- **ESLint**: Follow project rules; don't generate violations
- **Prettier / Biome**: Formatting will be auto-applied
- **TypeScript compiler**: Zero errors is the baseline

Reference: Vanderkam's *Effective TypeScript*
