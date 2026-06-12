---
description: Apply the refactoring catalog (Fowler) and legacy-rescue techniques (Feathers) to existing code
name: refactor
---

# Refactor

Improve existing code without changing behavior. Pick the catalog move that fits, name it, apply it, run the tests.

## Before Touching Anything

- **Tests exist?** If yes, run them green first. If no, write characterization tests *before* changing structure.
- **One change at a time.** Never combine behavior change with structural change in the same step.
- **Commit between moves.** Small, reversible steps; each commit compiles and passes tests.

## Refactoring Catalog (Fowler)

Pick the move that matches the smell. Always name it and say *why* it improves the design.

| Move | When to apply |
|---|---|
| **Extract Method** | Long method or block that does one nameable thing |
| **Extract Class** | One class has multiple reasons to change |
| **Extract Interface** | Concrete dependency blocks testability or substitution |
| **Replace Conditional with Polymorphism** | `switch` / chain of `if` over a type code |
| **Introduce Parameter Object** | The same parameter cluster recurs |
| **Replace Constructor with Factory Method** | Construction needs a name or selection logic |
| **Move Method / Move Field** | Feature envy: method touches another class's data more than its own |
| **Replace Inheritance with Delegation** | Fragile hierarchy; "is-a" was wishful |
| **Inline Method / Inline Class** | Indirection adds nothing |
| **Rename** | Name doesn't reveal intent |

Reference: Fowler, *Refactoring* (Ch. 6–12); Kerievsky, *Refactoring to Patterns*.

## Tidy First Moves (Beck)

Smaller than catalog moves. Often enough on their own.

| Move | When |
|---|---|
| **Guard clauses** | Nested conditionals hide the happy path |
| **Slide statements** | Related code is scattered |
| **Split phase** | One function does two distinct things in sequence |
| **Extract helper** | Block deserves a name |
| **Rename** | Name lies, hedges, or is generic |

## Working with Legacy Code (Feathers)

Legacy = code without tests. Goal: make it safe to change.

### Get it under test first
- **Characterization tests**: document current behavior, even if wrong. "What does it do?" not "What should it do?"
- **Find seams**: places behavior can change without editing the existing code — object seams, preprocessing seams, link seams.
- **Break dependencies safely**: Extract Interface, Parameterize Constructor, Adapt Parameter.

### Add behavior safely
- **Sprout Method / Sprout Class**: new logic in a new, tested unit; call it from the legacy caller.
- **Wrap Method / Wrap Class**: preserve existing behavior, add new behavior alongside.
- **Strangler pattern**: route new paths through clean code; let the old code shrink.

### Principles
- Never rewrite from scratch. Incrementally expand the tested surface.
- Legacy code is code we don't understand yet. Add understanding through tests *before* changing it.
- Each step leaves the code in a working state.

Reference: Feathers, *Working Effectively with Legacy Code* (Ch. 4 seams, Ch. 8 sprout/wrap, Ch. 25 dependency-breaking).

## Output Discipline

- For each move: **name it**, show the before/after diff, state the smell it addresses, and confirm tests still pass.
- Stop when the next planned change is easy. Perfection is the enemy of done.

What code should we refactor, and what's the next change we're making it easy for?
