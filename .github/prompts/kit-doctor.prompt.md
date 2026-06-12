---
description: Self-audit this Copilot kit for token cost, bloat, schema drift, and coverage gaps
name: kit-doctor
---

# Kit Doctor

Audit `.github/` (and `.vscode/` when present) and produce an actionable report. Read-only — don't edit files, just diagnose. Prefer measured numbers over vibes.

## Stance

This kit pays a token tax on every Copilot turn. The job here is to keep that tax honest and the structure healthy. Treat the kit the same way you'd treat a production codebase: measure first, prescribe second, never fix without explicit user approval afterwards.

## What to Check

### 1. Token cost

- Run `wc -c .github/copilot-instructions.md .github/instructions/*.md .github/prompts/*.md .github/chatmodes/*.md` and report bytes per file.
- Convert to rough tokens with `bytes / 4` (good enough for prose).
- Build a **steady-state cost matrix**: for each common file-type-in-context scenario, sum the auto-loaded instructions (the always-on file plus every `applyTo` match).

Scenarios to compute:

| Scenario | Files in context |
|---|---|
| Plain TypeScript | `*.ts` |
| Plain Java (no Spring) | `*.java` |
| Spring Java | `*.java` + Spring config |
| Python | `*.py` |
| CDK TypeScript | `cdk.json` + `lib/*-stack.ts` |
| Test file (any language) | matches xp instructions' applyTo |
| Astro page | `.astro` |
| AWS-only chat (no project files) | only `copilot-instructions.md` |

Report each scenario's total in tokens. Flag any scenario > **6,000 tokens** as a budget overrun.

### 2. Bloat & duplication

- Flag any auto-loaded file (one matched by any reasonable `applyTo`) over **1,800 tokens**.
- Grep for duplicated section headings across instruction files (e.g., the same `## SOLID` or `## Testing` block appearing in two places).
- Grep for cross-file "see also" preambles ("Design principles are in design.instructions.md") — these are paid context the model can't follow as links.
- Grep for prose markers that suggest essay rather than rules: `Why it matters:`, long block quotes, full-sentence URLs, ASCII diagrams. None are automatic kills, but each one is a candidate for compression or for moving to a prompt.

### 3. Aspirational schema

GitHub Copilot does not honor every frontmatter field people put in their files. Detect and flag these:

- `agent:` in prompt frontmatter.
- `handoffs:` anywhere.
- `tools:` arrays in `prompts/` files (only valid in chat modes).
- Chat modes with no `tools:` array at all (likely missing capability).
- Chat modes with fewer than 5 tools listed — the model often reads narrow lists as "these are my only hands."
- Instruction files missing `applyTo` (they will never auto-load).

### 4. Structural integrity

- Code fences open without closing.
- Frontmatter that doesn't parse (e.g., unbalanced YAML).
- Markdown tables with mismatched column counts.
- File names that don't match the convention: `*.instructions.md`, `*.prompt.md`, `*.chatmode.md`.

### 5. Coverage gaps

- Instructions that reference a prompt (`/refactor`, `/expert-panel`, etc.) where the prompt file doesn't exist.
- Languages in the README's tooling table that don't have an instruction file.
- Languages with an instruction file but missing from the README's table.
- The Audience section in `copilot-instructions.md` lists stacks (`aws`, `cdk`, etc.) — verify each one has a matching instruction file.

### 6. Chat mode additive-stance check

For each chat mode file:
- Does it have an explicit "Stance" section naming itself as additive?
- Does it have a "Stay Capable" (or equivalent) reminder of pivots?
- Does it use "Prefer" / "Default to" framing rather than "Don't" lists?
- Is the `tools:` array generous (≥ 5 entries)?

Flag any chat mode that fails two or more of these.

## Output Format

Produce a single markdown report with these sections, in this order:

```
# Kit Doctor Report

Generated: <date>

## Headline

Top 3 findings, ranked by impact. One line each.

## Steady-state cost matrix

| Scenario | Tokens | Status |
|---|---:|---|
| Plain TS | 2,400 | OK |
| Spring Java | 5,200 | OK |
| CDK TypeScript | 5,100 | OK |
| ... | | |

## Per-file sizes

| File | Bytes | Tokens (~) | Auto-load trigger | Status |
|---|---:|---:|---|---|

## Findings

### Blockers
- Concrete, with file:line refs.

### Should-fix
- Concrete, with file:line refs.

### Nits
- Concrete, with file:line refs.

## Recommended next moves

A ranked TODO list with rough effort and rough token savings per item.

## What looks healthy

A short list. Don't pad it — only mention things genuinely worth noting.
```

## Discipline

- Do not edit any kit file during this audit. Report only.
- Don't speculate about token cost — measure with `wc -c`.
- When flagging duplication, quote the duplicated lines from both files so the user can verify.
- When flagging aspirational schema, name the field and the file:line so the user can grep.
- If a "finding" is actually a deliberate choice the user made, the user will tell you on the next turn. That's fine — the doctor recommends, the user decides.

## After the Report

Ask: **"Want me to apply any of these? If yes, which ones, in what order?"**

Then wait. Do not auto-apply.
