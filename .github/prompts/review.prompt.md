---
description: Review code or a PR diff for correctness, design, smells, and missing tests
name: review
---

# Review

Review the code (or PR diff) I'm pointing you at. Pick the mode that fits.

## Code Review Mode

When reviewing code in isolation:

1. Summarize what the code does.
2. Rate it on the Code Quality Spectrum (convoluted → elegant) and name the current level honestly.
3. Identify code smells by name (Fowler, *Refactoring* Ch. 3). Don't gesture at them — name them.
4. Suggest specific refactorings with rationale. Use the named moves from the catalog (Extract Method, Replace Conditional with Polymorphism, etc.).
5. Point out SOLID violations where they actually exist; don't manufacture them.
6. Note missing tests or untested edge cases.
7. Explain trade-offs between options when there are real ones.
8. Cite principles with attribution when you invoke them ("Item 18, *Effective Java*"; "Ch. 3, *Refactoring*").
9. Prioritize: correctness > clarity > simplicity > performance.

## PR Review Mode

When reviewing a pull request or diff:

1. **Intent**: what problem is this PR solving? State it in one sentence before reviewing the code.
2. **Does it do what it claims?** Gaps, off-by-one, half-finished branches.
3. **Quality Spectrum rating** for the changed code.
4. **Smells introduced** + missed opportunities to improve existing code in the diff's neighborhood.
5. **Test coverage**: are new behaviors tested? Edge cases? Are the tests load-bearing or vanity?
6. **Flag**: SRP violations, mutating inputs, void side-effect methods, names that lie.
7. **Suggest concrete improvements** with the refactoring's name + a one-line rationale.
8. **Future-cost**: anything that makes the code harder to change later.
9. **Acknowledge what's done well** before suggesting improvements — but briefly. The author wrote the diff; they don't need a parade.

## Output Discipline

- One section per finding. Lead with severity (`blocker` / `should-fix` / `nit`).
- File:line references for every concrete finding.
- No "consider possibly maybe perhaps" — say what you'd change and why.

For multi-expert perspectives on the same code, use `/expert-panel` instead.
