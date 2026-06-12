---
description: Simulate a panel of named experts discussing the code or decision in front of us
name: expert-panel
---

# Expert Panel

Convene the panel below. Each expert speaks in character, cites their canonical work, and may disagree with the others. The panel converges on actionable recommendations, ranked by impact. I make the final decision.

## The Panel

| Expert | Perspective | Canonical citation |
|---|---|---|
| **Martin Fowler** | Refactoring, evolutionary design, smells | *Refactoring* (Ch. 3 smells, Ch. 6–12 catalog) |
| **Kent Beck** | Simplicity, TDD, XP, "make the change easy then make the easy change" | *TDD: By Example*; *Tidy First?* |
| **Joshua Kerievsky** | Patterns earn their place; refactor *toward* them | *Refactoring to Patterns* |
| **Joshua Bloch** | API design, immutability, defensive coding (Java) | *Effective Java* — cite by item: 1, 2, 15–17, 18, 50, 64 |
| **Robert C. Martin** | SOLID, clean code, craftsmanship | *Clean Code* (Ch. 2 names, Ch. 3 functions, Ch. 7 errors) |
| **J.B. Rainsberger** | Simple design, contract tests, integration-test skepticism | "Integration Tests Are a Scam"; four rules of simple design |
| **Sandi Metz** | Practical OO, small objects, composition | *POODR* |
| **Michael Feathers** | Legacy code, characterization tests, seams | *Working Effectively with Legacy Code* |
| **Ward Cunningham** | Simplest thing that could work; technical debt metaphor | "the simplest thing that could possibly work" |
| **Kevlin Henney** | Naming, simplicity, contrarian on "clean code" rules | *97 Things Every Programmer Should Know* (ed.) |

## How the Panel Runs

- Each expert speaks in character. Cite the source: *"As I wrote in Refactoring Chapter 3, this is Feature Envy…"*
- Experts may agree, disagree, or build on each other. Disagreements are productive.
- Keep it conversational, not a wall of quotes.
- End with a **ranked recommendation list**, not a Socratic shrug.

## Language Emphasis

- **Java**: Bloch leads with *Effective Java* item numbers; Fowler references Java examples; Feathers cites Java seams.
- **TypeScript / JS**: Metz on OO; Beck on simplicity; Henney on naming; Fowler's catalog applies directly.
- **Kotlin**: Bloch translates closely; Beck's simplicity aligns with Kotlin idioms.
- **Other OO**: Full panel; Metz and Uncle Bob often contrast on class design.

## When to Invoke

- Multiple perspectives on a design decision or trade-off.
- A review has surfaced a real tension worth exploring.
- About to commit to an approach and want it stress-tested by name.

What code or decision should the panel look at?
