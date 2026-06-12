---
description: Self-audit this Copilot kit for token cost, bloat, schema drift, and coverage gaps
name: kit-doctor
---

# Kit Doctor

Audit `.github/` (and `.vscode/` when present). Read-only — diagnose, don't edit. Measure with `wc -c`; don't estimate.

## Stance

This kit pays a token tax on every Copilot turn. Keep it honest. Treat the kit like a production codebase: measure first, prescribe second, never apply without explicit user approval.

## Checks

- **Token cost.** Run `wc -c .github/copilot-instructions.md .github/instructions/*.md .github/prompts/*.md .github/chatmodes/*.md`. Convert with `bytes ÷ 4`. Build a steady-state cost matrix for: Plain TS, Plain Java, Spring Java, Python, CDK TS, Test file, Astro page, Chat-only.
- **Auto-load budget.** Flag any instruction file over **1,800 tok** that's matched by any reasonable `applyTo`.
- **Scenario budget.** Flag any scenario over **6,000 tok** of background context.
- **Duplication.** Grep for repeated section headings across instruction files. Quote both occurrences when flagging.
- **Cross-file preambles.** Lines like "Design principles are in design.instructions.md" — the model can't follow them as links, so they're paid context.
- **Prose markers** that suggest essay rather than rules: `Why it matters:`, long block quotes, full-sentence URLs, ASCII diagrams. Candidates for compression, not automatic kills.
- **Aspirational schema.** `agent:` or `handoffs:` anywhere. `tools:` in `prompts/` files. Instruction files missing `applyTo`. Chat modes missing `tools:` or carrying fewer than 5 entries.
- **Structural integrity.** Unbalanced code fences, unparseable frontmatter, mismatched table columns, files that don't match the `*.instructions.md` / `*.prompt.md` / `*.chatmode.md` convention.
- **Coverage gaps.** Instructions referencing a `/prompt` whose file doesn't exist. Languages in README's tooling table without an instruction file (or vice versa). Stacks declared in the Audience section of `copilot-instructions.md` without a matching instruction file.
- **Chat-mode additive stance.** Each chat mode should carry a `## Stance` section *and* a `## Stay Capable` (or equivalent) section, use "Prefer"/"Default to" framing rather than "Don't" lists, and have a generous `tools:` array (≥ 5).

## Report

Produce one markdown document with these sections, in this order:

- **Headline** — top 3 findings ranked by impact, one line each.
- **Steady-state cost matrix** — scenario / tokens / status table.
- **Per-file sizes** — bytes, tokens, applyTo trigger, status. Sort by tokens descending.
- **Findings** — grouped as Blockers / Should-fix / Nits. Every finding cites file:line. Quote duplicated text where applicable.
- **Recommended next moves** — ranked TODO with rough effort and rough savings per item.
- **What looks healthy** — short, only genuinely worth noting.

## Discipline

- Don't edit any kit file during this audit.
- Don't speculate about token cost — measure.
- When flagging duplication, quote both sides so the user can verify.
- When flagging aspirational schema, name the field and file:line.
- If a "finding" is a deliberate choice the user made, they'll say so — the doctor recommends, the user decides.

After the report, ask: **"Want me to apply any of these? If yes, which ones, in what order?"** Then wait.
