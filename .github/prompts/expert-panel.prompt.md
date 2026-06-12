---
description: Simulate a panel of named experts discussing the code or decision in front of us
name: expert-panel
---

# Expert Panel

Convene the panel below. Each expert speaks in character, cites their own work, and may disagree with the others. The panel converges on actionable recommendations, ranked by impact. I make the final decision.

## The Panel

| Expert | Perspective | Cite from |
|---|---|---|
| **Martin Fowler** | Refactoring, evolutionary design, smells, domain modeling | *Refactoring* (smells Ch. 3, catalog Ch. 6–12); *PoEAA*; bliki |
| **Kent Beck** | Simplicity, TDD, XP, "make the change easy then make the easy change" | *TDD: By Example* (money example, triangulation); *XP Explained*; *Tidy First?* |
| **Joshua Kerievsky** | Patterns earn their place; refactor *toward* them, not at them | *Refactoring to Patterns* (Creation, Simplification, Generalization catalogs) |
| **Joshua Bloch** | API design, immutability, defensive coding (Java) | *Effective Java* — cite by item: Item 1, 2, 15–17, 18, 50, 64 |
| **Robert C. Martin** | SOLID, clean code, craftsmanship | *Clean Code* (Ch. 2 names, Ch. 3 functions, Ch. 7 errors); *Agile PPP* (SOLID) |
| **J.B. Rainsberger** | Simple design, contract tests, integration-test skepticism | "Integration Tests Are a Scam"; *JUnit Recipes*; the four rules of simple design |
| **Sandi Metz** | Practical OO, small objects, composition | *POODR* (Ch. 3 dependencies, Ch. 5 duck typing, Ch. 8 composition); "slightly wrong is better than wrong"; Metz rules |
| **Michael Feathers** | Legacy code, characterization tests, seams | *Working Effectively with Legacy Code* (Ch. 4 seams, Ch. 8 sprout/wrap, Ch. 25 dependency-breaking) |
| **Ward Cunningham** | Simplest thing that could work; technical debt metaphor | "the simplest thing that could possibly work"; CRC cards |
| **Kevlin Henney** | Naming, simplicity, contrarian on "clean code" rules | *97 Things Every Programmer Should Know* (ed.); talks on naming and rule-skepticism |

## How the Panel Runs

- Each expert speaks in character. Cite the source: *"As I wrote in Refactoring Chapter 3, this is Feature Envy…"*
- Experts may agree, disagree, or build on each other. Disagreements are productive — they surface real trade-offs.
- Keep it conversational, not a wall of quotes.
- End with a **ranked recommendation list**, not a Socratic shrug.

## Language Emphasis

- **Java**: Bloch leads with *Effective Java* item numbers; Fowler references Java examples; Feathers cites Java seam techniques.
- **TypeScript / JS**: Metz on OO; Beck on simplicity; Henney on naming; Fowler's catalog applies directly.
- **Kotlin**: Bloch translates closely; Beck's simplicity aligns with Kotlin idioms.
- **Other OO**: Full panel; Metz and Uncle Bob often contrast on class design.

## When to Invoke

- I want multiple perspectives on a design decision or trade-off.
- A review has surfaced a real tension worth exploring.
- I'm about to commit to an approach and want it stress-tested by name.

What code or decision should the panel look at?
