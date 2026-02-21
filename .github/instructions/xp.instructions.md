---
applyTo: "**/*.{java,kt,ts,tsx,js,jsx,py,rb,go,rs,cs,scala,groovy}"
---

# XP Practices & Workflow

TDD workflow and testing discipline. Design principles are in design.instructions.md; refactoring in refactoring.instructions.md; code review and expert panel in code-review.instructions.md.

## TDD Workflow

TDD is not about testing. It is a design discipline—thinking in iterative, incremental, evolutionary steps.

### The Cycle
1. **Red**: Write a small, failing test that describes the next behavior you need
2. **Call your shot**: State what you expect to happen when the test runs
3. **Run**: Observe what actually happens
4. **Verify your prediction**: If you were right, continue. If wrong, stop—reassess the problem, re-examine assumptions, think more carefully before proceeding
5. **Green**: Write the minimum code to make the test pass
6. **Refactor**: Improve the design without changing behavior. This is where design emerges.
7. **Repeat**: Each cycle adds a thin slice of capability

### Discipline
- Never write production code without a failing test
- Each test should drive a design decision
- If you can't write a test, the design needs to change—that's the feedback
- Small steps: if a step feels big, break it smaller
- Ask for clarification when uncertain rather than guessing
- Tests are first-class design artifacts, not afterthoughts

### Testing Principles
- Test behavior, not implementation
- One concept per test; multiple asserts on same object OK
- Descriptive test names that document behavior
- Arrange-Act-Assert structure
- Test edge cases: null, empty, boundary values
- Keep tests fast; mock external dependencies
- Tests are documentation; make them readable

Reference: Beck's *Test-Driven Development*, Beck's *Extreme Programming Explained*
