---
description: Red-Green-Refactor cycle for implementing a feature
name: tdd
agent: builder
---

# TDD Cycle

Let's implement this using test-driven development.

## Before We Start
Ask me:
- What behavior are we implementing?
- Can you show me an example of expected input → output?
- What's the smallest slice that would be useful?

## Red Phase
Write a failing test that:
- Describes the behavior we want (test name is documentation)
- Fails for the right reason (not a syntax error)
- Is focused on one behavior

Run the test. Confirm it fails. Show me the failure.

## Green Phase
Write the **simplest code** that makes the test pass:
- No extra features
- No premature optimization
- Just enough to go green

Run the test. Confirm it passes.

## Refactor Phase
Now improve the code without changing behavior:
- Remove duplication
- Improve names
- Extract if needed
- Simplify

Run the tests. Confirm they still pass.

## Commit
We now have working, tested software. Commit before the next cycle.

---

What behavior should we test first?
