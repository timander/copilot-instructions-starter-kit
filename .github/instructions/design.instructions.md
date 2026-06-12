---
applyTo: "**/*.{java,kt,ts,tsx,js,jsx,py,rb,go,rs,cs,scala,groovy}"
---

# Design

Language-agnostic rules for any source file. Auto-loaded — keep terse. Deeper catalogs live in `/refactor` and `/expert-panel`.

## Code Quality Spectrum

When assessing code, name the level honestly:

| Level | Meaning |
|---|---|
| **Convoluted** | Tangled. The author didn't understand it or didn't want others to. |
| **Overly complex** | More moving parts than the problem needs. |
| **Complicated** | May work; hard to change safely. |
| **Clever** | Impresses; confuses the next reader. Warning sign. |
| **Sophisticated** | Well-structured solution to genuine complexity. |
| **Clean** | Clear intent, well-tested, follows conventions. |
| **Elegant** | Cannot be meaningfully improved. The reference example. |

Aim for elegant. Name the current level and what would move it one step closer.

## Simplicity

- The best code is code that doesn't exist. Delete what isn't needed.
- Solve the problem at hand, not the hypothetical future one.
- "What is the simplest thing that could possibly work?" (Cunningham)
- "Make the change easy, then make the easy change." (Beck)
- Every abstraction has a cost. Earn it with duplication or proven need — not anticipation.
- If a junior can't understand it in 5 minutes, simplify.

## Clarity

- Code is read far more than written. Optimize for the reader.
- A function reads like a paragraph: clear purpose, logical flow, no surprises.
- If code needs a comment to explain *what* it does, rename or restructure until it doesn't.
- Short, descriptively-named methods are the unit of readability.

## SOLID (one-liners)

Apply when relevant; explain the principle, don't just name it:

- **SRP** — one reason to change.
- **OCP** — extend without modifying. Favor composition.
- **LSP** — subtypes substitutable for their base.
- **ISP** — small focused interfaces; no forced dependence on unused methods.
- **DIP** — depend on abstractions; inject via constructor.

## Code Smells (flag and name)

- **Modifying input parameters** — return new values; treat inputs as read-only.
- **Void methods with side effects** — prefer return values.
- **SRP violation** — class does too much.
- **Long method** (>20 lines) — extract.
- **Long parameter list** (>3) — parameter object or builder.
- **Feature envy** — method uses another class's data more than its own. Move it.
- **Data clumps** — same fields recur together. Extract a type.
- **Primitive obsession** — use domain types, not raw primitives.
- **God class** — split by responsibility.
- **Speculative generality** — delete unused abstractions.
- **Dead code** — delete.

## Generation Rules

- No comments. Self-documenting code through names.
- Think before generating; explain reasoning briefly when non-obvious.
- Consider how a change affects the broader codebase.
- When forced to choose: clear over concise.
