---
name: kit-doctor
description: Self-audit this Copilot kit for token cost, bloat, schema drift, and coverage gaps. Use when the user wants to verify the kit's health or before adopting changes.
allowed-tools: shell
---

# Kit Doctor

Audit the kit directories (`~/.copilot-kit/` for the workstation install, or `.github/` in a repo). Read-only — diagnose, don't edit. Measure with `wc -c`; don't estimate.

## Stance

This kit pays a token tax on every Copilot turn. Keep it honest. Treat the kit like a production codebase: measure first, prescribe second, never apply without explicit user approval.

## Checks

- **Token cost.** Run `wc -c` over the kit's instructions, agents, and skill files. Convert with `bytes ÷ 4`. Build a steady-state cost matrix for: Plain TS, Plain Java, Spring Java, Python, CDK TS, Test file, Astro page, Chat-only.
- **Auto-load budget.** Flag any instruction file over **1,800 tok** that's matched by any reasonable `applyTo`.
- **Scenario budget.** Flag any scenario over **6,000 tok** of background context.
- **Duplication.** Grep for repeated section headings across instruction files. Quote both occurrences when flagging.
- **Cross-file preambles.** Lines like "Design principles are in design.instructions.md" — the model can't follow them as links, so they're paid context.
- **Prose markers** that suggest essay rather than rules: `Why it matters:`, long block quotes, full-sentence URLs, ASCII diagrams. Candidates for compression, not automatic kills.
- **Aspirational schema.** `agent:` or `handoffs:` anywhere in prompts. Instruction files missing `applyTo`. Agents with malformed or empty `tools:` arrays.
- **Structural integrity.** Unbalanced code fences, unparseable frontmatter, mismatched table columns, files that don't match the `*.instructions.md` / `*.agent.md` / `SKILL.md` convention.
- **Coverage gaps.** Instructions referencing a skill or agent whose file doesn't exist. Languages declared in the README's tooling table without an instruction file. Stacks declared in the Audience section of `copilot-instructions.md` without a matching instruction file.
- **Agent additive stance.** Each agent should carry a `## Stance` section *and* a `## Stay Capable` (or equivalent) section, use "Prefer"/"Default to" framing rather than "Don't" lists, and either have a generous `tools:` array or omit it entirely (default = all tools).

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
