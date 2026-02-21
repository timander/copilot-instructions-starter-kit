---
applyTo: "**/*.{java,kt,ts,tsx,js,jsx,py,rb,go,rs,cs,scala,groovy}"
---

# Design Principles

Fundamental principles for writing software. These are language-agnostic and apply to all code.

## Code Quality Spectrum

Use this vocabulary when assessing or describing code:

| Level | Meaning |
|-------|---------|
| **Convoluted** | Disorganized or needlessly tangled; suggests the author lacked understanding or intentionally made the code hard to work with |
| **Overly complex** | More moving parts than the problem requires; needs simplification |
| **Complicated** | Hard to follow; may work but is difficult to change safely |
| **Clever** | Uses tricks that impress but confuse the next reader; cleverness is a warning sign |
| **Sophisticated** | Well-structured solution to genuine complexity; appropriate use of patterns |
| **Clean** | Clear intent, easy to read, well-tested, follows conventions |
| **Elegant** | The highest compliment. Cannot be meaningfully improved. Would share as an example of what good code looks like. |

Always strive toward **elegant**. When reviewing, name the current level honestly and explain what would move the code one level closer to elegant.

## Simplicity

Simplicity is the ultimate sophistication. It is harder to achieve than complexity.
- The best code is code that doesn't exist; delete what isn't needed
- Solve the problem at hand, not hypothetical future problems
- "What is the simplest thing that could possibly work?" (Cunningham)
- "Make the change easy, then make the easy change" (Beck)
- Every abstraction has a cost; earn it with duplication or proven need
- If a junior developer can't understand it in 5 minutes, simplify it

## Clarity & Readability

Readability is not optional—it is a design requirement.
- Code is read far more often than written; optimize for the reader
- Prefer simple, obvious solutions over clever ones
- A method/function should read like a paragraph: clear purpose, logical flow, no surprises
- If code needs a comment to explain *what* it does, rename or restructure until it doesn't
- Short methods with descriptive names are the primary unit of readability
- Consistent formatting, naming, and structure reduce cognitive load

## SOLID Principles

Apply and explain when relevant:
- **Single Responsibility**: One reason to change. Extract classes/modules when responsibilities diverge.
- **Open/Closed**: Extend behavior without modifying existing code. Favor composition.
- **Liskov Substitution**: Subtypes must be substitutable for their base types.
- **Interface Segregation**: Small, focused interfaces. Clients shouldn't depend on unused methods.
- **Dependency Inversion**: Depend on abstractions. Inject dependencies via constructor.

## Code Smells

Flag these patterns and explain the problem:
- **Modifying input parameters**: Return new values instead; inputs should be read-only
- **Void methods with side effects**: Prefer methods that return results; easier to test and reason about
- **SRP violations**: Class/module doing too much; split by responsibility
- **Long methods** (>20 lines): Extract smaller, named methods
- **Long parameter lists** (>3): Introduce parameter object or builder
- **Feature Envy**: Method uses another class's data more than its own; move it
- **Data Clumps**: Same fields appear together; extract a type
- **Primitive Obsession**: Use domain types instead of raw primitives
- **God Class**: Too many responsibilities; break apart
- **Speculative Generality**: Remove unused abstractions
- **Dead Code**: Delete unreachable or unused code

Reference: Fowler's *Refactoring*, Kerievsky's *Refactoring to Patterns*

## Design Patterns

Name patterns when suggesting them. Common applicable patterns:
- **Strategy**: Replace conditionals with polymorphism
- **Factory Method**: Encapsulate object creation
- **Builder**: Construct complex objects step-by-step
- **Decorator**: Add behavior without subclassing
- **Template Method**: Define algorithm skeleton, let subtypes override steps
- **Observer**: Decouple event producers from consumers
- **Adapter**: Bridge incompatible interfaces

## Generation Rules

- Never generate comments; code should be self-documenting
- Use descriptive names instead of comments
- Think carefully about design before generating
- Explain reasoning as a mentor would
- Reference relevant principles when they apply in the moment (SOLID, DRY, YAGNI, etc.)
- Consider how changes affect the broader codebase
- Prioritize readability: if you have to choose between concise and clear, choose clear

Reference: Hunt & Thomas's *The Pragmatic Programmer*, Metz's *Practical Object-Oriented Design*
