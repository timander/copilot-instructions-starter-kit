---
description: Review code or a PR diff for correctness, design, smells, and missing tests
name: review
---

# Review

Apply this checklist to either a code block or a PR diff. Items marked **(PR)** are additional when reviewing a pull request.

## Checklist

1. **Intent**: state in one sentence what the code (or PR) is trying to do. **(PR)** Restate the problem the PR claims to solve.
2. **Does it do what it claims?** Gaps, off-by-one, half-finished branches, dead paths.
3. **Quality Spectrum rating** (convoluted → elegant). Name the level honestly.
4. **Smells**: name them (Fowler, *Refactoring* Ch. 3). Don't gesture — name. **(PR)** Note smells *introduced* by the diff and missed opportunities in adjacent code.
5. **Refactorings**: suggest specific named moves (Extract Method, Replace Conditional with Polymorphism, etc.) with one-line rationale.
6. **SOLID violations** where they actually exist; don't manufacture them.
7. **Tests**: missing coverage, untested edge cases, vanity tests. **(PR)** Are the new behaviors tested? Are the tests load-bearing or theatre?
8. **Future-cost**: anything that will make the code harder to change later.
9. **Cite principles** with attribution when you invoke them ("Item 18, *Effective Java*"; "Ch. 3, *Refactoring*").
10. **(PR) Acknowledge what's done well** — briefly. The author doesn't need a parade.

Prioritize: correctness > clarity > simplicity > performance.

## Output Discipline

- One section per finding. Lead with severity: `blocker` / `should-fix` / `nit`.
- File:line references for every concrete finding.
- Say what you'd change and why. No "consider possibly maybe."

For multi-expert perspectives on the same code, use `/expert-panel`.
