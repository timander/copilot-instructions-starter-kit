---
applyTo: "**/*.{java,kt,ts,tsx,js,jsx,py,rb,go,rs,cs,scala,groovy}"
---

# Refactoring & Legacy Code

Improving existing code through safe, incremental transformation. Design principles are in design.instructions.md.

## Evolutionary Design

Software design emerges through small, safe, incremental improvements—not upfront planning.
- Each commit should compile and pass tests
- Make the change easy, then make the easy change
- Refactor continuously: small tidying, not big rewrites
- Never change behavior and structure in the same step

## Refactoring Catalog

Common refactorings to suggest. Always explain *why* the refactoring improves the design:
- **Extract Method**: Long method → smaller named methods that reveal intent
- **Extract Class**: Class with multiple responsibilities → focused collaborators
- **Extract Interface**: Concrete dependency → abstraction for testability and flexibility
- **Replace Conditional with Polymorphism**: Complex if/else or switch → strategy objects
- **Introduce Parameter Object**: Repeated parameter groups → cohesive type
- **Replace Constructor with Factory Method**: Complex construction → named creation methods
- **Move Method / Move Field**: Feature envy → move behavior to where the data lives
- **Replace Inheritance with Delegation**: Fragile hierarchy → composition
- **Inline Method / Inline Class**: Unnecessary indirection → simplify
- **Rename**: Unclear name → name that reveals intent

Reference: Fowler's *Refactoring*, Kerievsky's *Refactoring to Patterns*

## Working with Legacy Code (Feathers)

Legacy code is code without tests. The goal is to make it safe to change.

### Getting Code Under Test
- **Characterization tests first**: Before changing anything, write tests that document current behavior—even if that behavior is wrong
- **Find seams**: Identify points where behavior can be changed without editing existing code (object seams, preprocessing seams, link seams)
- **Break dependencies safely**: Use Extract Interface, Parameterize Constructor, or Adapt Parameter to make code testable

### Adding Behavior Safely
- **Sprout Method/Class**: Put new logic in a new, tested method or class; call it from legacy code
- **Wrap Method/Class**: Preserve existing behavior while adding new behavior alongside it
- **Strangler pattern**: Incrementally replace legacy code by routing new paths through clean code

### Principles
- Never rewrite from scratch; improve incrementally with test coverage expanding at each step
- Legacy code without tests is just code we don't understand yet—add understanding through tests before changing it
- Each refactoring step should leave the code in a working state
- Expand the tested surface area with every change

Reference: Feathers' *Working Effectively with Legacy Code*

## When to Refactor

- **Before adding a feature**: Make the code easy to change first (tidy first)
- **After getting a test green**: The Refactor step in Red-Green-Refactor
- **When you see a code smell**: Don't walk past it; leave the code better than you found it
- **During code review**: Suggest refactorings with named patterns and rationale
- **Never during an emergency**: Get to green first, then improve

## Refactoring Safety

- Run tests after every refactoring step
- Use IDE automated refactorings when available—they are safer than manual edits
- Commit after each successful refactoring; keep changes small and reversible
- If a refactoring breaks tests, undo immediately and take a smaller step
