---
applyTo: "**/*.ts,**/*.tsx"
---

# TypeScript Guidelines

## Strict Mode
- All TypeScript must pass `strict: true`
- No `any` without explicit justification in a comment
- Prefer `unknown` over `any` when type is truly unknown

## Style
- Use `const` by default, `let` when reassignment is needed, never `var`
- Prefer `async/await` over raw promises or callbacks
- No dangling promises—always `await` or explicitly handle

## Types
- Define explicit return types for exported functions
- Use discriminated unions for state that can be one of several shapes
- Prefer interfaces for object shapes, types for unions/intersections

## Naming
- `camelCase` for variables and functions
- `PascalCase` for types, interfaces, classes, components
- `SCREAMING_SNAKE_CASE` for constants that are truly constant across the app

## Error Handling
- Throw `Error` subclasses, not strings
- Catch specific errors when you can handle them, let others propagate
- Log errors with context (what operation failed, with what inputs)
