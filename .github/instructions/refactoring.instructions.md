---
applyTo: "**/*.{java,kt,ts,tsx,js,jsx,py,rb,go,rs,cs,scala,groovy}"
---

# Refactoring Stance

Auto-loaded everywhere. Full catalog (Fowler + Beck + Feathers) lives in `/refactor`.

## Evolutionary Design

- Each commit compiles and passes tests.
- "Make the change easy, then make the easy change." (Beck)
- Refactor continuously: small tidying, not big rewrites.
- **Never change behavior and structure in the same step.**

## When to Refactor

- **Before a feature** — tidy first so the next change is easy.
- **After a green test** — the Refactor step of red-green-refactor.
- **When you see a smell** — leave it better than you found it.
- **Never during an emergency** — get to green first.

## Safety

- Run tests after every step.
- Prefer IDE-automated refactorings; they're safer than manual edits.
- Commit after each successful step. Keep changes small and reversible.
- If a refactoring breaks tests, undo immediately and take a smaller step.

For the named-move catalog and legacy-rescue techniques, invoke `/refactor`.
