# Copilot Instructions Starter Kit

A ready-to-use template for GitHub Copilot custom instructions, prompts, and agents — optimized for the metered-pricing era. Tight always-on context; deep content sits behind on-demand prompts.

**Read the full guide:** [Keep Your AI on a Leash](https://timandersen.net/post/copilot-custom-instructions)

## Quick Start

Copy the `.github` folder to your project:

```bash
cd your-project
curl -L https://github.com/timander/copilot-instructions-starter-kit/archive/main.tar.gz | tar -xz --strip-components=1 copilot-instructions-starter-kit-main/.github
```

Or clone and copy manually:

```bash
git clone --depth 1 https://github.com/timander/copilot-instructions-starter-kit.git /tmp/copilot-starter
cp -r /tmp/copilot-starter/.github your-project/
rm -rf /tmp/copilot-starter
```

## Three Building Blocks

| Concept | What It Is | When It Activates | Think of It As |
|---|---|---|---|
| **Instructions** | Background rules injected when files matching `applyTo` are in context | Automatic | Team standards on the wall |
| **Prompts** | Reusable conversation starters invoked by name | On-demand — type `/tdd`, `/review`, etc. | A checklist you pull out |
| **Chat modes** | A suggested approach for the conversation (Copilot Chat custom modes) | On-demand — picked from the chat mode dropdown | A rhythm you lean into, not a role you lock into |

**Instructions** shape *how* Copilot behaves. **Prompts** shape *what* Copilot asks. **Chat modes** suggest *which approach* to lean into for the activity at hand — without giving up other capabilities.

### Chat modes are additive, not limiting

A chat mode is an approach the model leans into, not a persona that replaces its other skills. If you're in `build` and the conversation turns out to be a debugging session, you don't need to switch modes — the model can pivot. Each chat mode says this out loud in its body so the model doesn't tunnel-vision.

The trap to avoid when authoring new chat modes:

- ❌ "You are a TDD specialist" — role-as-noun. Sticks. Excludes everything else.
- ✅ "Lean into TDD when adding new behavior" — verb-as-approach. Pivotable.
- ❌ Long "Don't" lists — read as hard constraints.
- ✅ "Prefer X" / "Default to Y" — read as defaults.
- ❌ Narrow `tools:` arrays — read as "these are my only hands now."
- ✅ Generous `tools:` arrays — preserve capability.

Every chat mode in this kit opens with a "Stance" section that names this principle.

## Token Discipline

GitHub Copilot's pricing is now metered. Every file that auto-loads costs tokens on every turn. This kit is deliberately structured so:

- `copilot-instructions.md` (always on) is small and stable.
- Auto-loaded instruction files contain **rules**, not rationale. Brief reasons only where they change behavior.
- Heavy content — refactoring catalogs, expert-panel discussions, full code reviews — sits behind **prompts** that load only when invoked.
- `xp.instructions.md` is scoped to test files only; you don't pay for TDD doctrine when you're renaming a variable.

A typical Spring Java turn went from ~11,300 tokens of background context to ~5,200. A plain TypeScript turn is ~2,400.

## What's Included

```
.github/
├── copilot-instructions.md              # Always on — identity, workflow, interaction style
│
├── instructions/                        # Auto-loaded when files match applyTo
│   ├── design.instructions.md           # Code Quality Spectrum, smells, simplicity, SOLID one-liners
│   ├── refactoring.instructions.md      # Evolutionary design + when/how to refactor safely
│   ├── xp.instructions.md               # Testing principles (test files only)
│   ├── java.instructions.md             # Java syntax, version detection, Effective Java, optional tooling
│   ├── kotlin.instructions.md           # Kotlin idioms, version detection, optional tooling
│   ├── scala.instructions.md            # Scala 2/3, idioms, optional tooling
│   ├── python.instructions.md           # Idiomatic Python, pytest, UV, optional tooling
│   ├── ruby.instructions.md             # Idiomatic Ruby, Bundler, optional tooling
│   ├── typescript.instructions.md       # TypeScript strict, types, testing framework detection
│   ├── spring.instructions.md           # Spring wiring, config, testing — rules-first
│   ├── aws.instructions.md              # Read-only AWS CLI; identity/region confirmation; CloudWatch idioms
│   ├── cdk.instructions.md              # CDK v2; synth/diff via project wrappers; never cdk deploy
│   └── astro.instructions.md            # Astro 5.x + TinaCMS project conventions
│
├── prompts/                             # User-invoked with /name
│   ├── tdd-cycle.prompt.md              # /tdd          — red-green-refactor
│   ├── review.prompt.md                 # /review       — code review or PR review mode
│   ├── refactor.prompt.md               # /refactor     — Fowler catalog + Feathers legacy techniques
│   ├── expert-panel.prompt.md           # /expert-panel — multi-expert discussion
│   ├── diagnose.prompt.md               # /diagnose     — systematic debugging
│   ├── challenge-me.prompt.md           # /challenge    — Socratic questioning
│   ├── audience.prompt.md               # /audience     — switch explanation level per stack
│   ├── kit-doctor.prompt.md             # /kit-doctor   — self-audit this kit (token cost, bloat, drift)
│   ├── code-quality.prompt.md           # /code-quality — opt-in lint/static-analysis/coverage tooling per language
│   └── options.prompt.md                # /options      — trade-offs laid out
│
└── chatmodes/                           # Picked from the chat mode dropdown
    ├── build.chatmode.md                # Lean into TDD rhythm when adding new behavior
    ├── diagnose.chatmode.md             # Sustained investigation; read-leaning (no edit tools)
    ├── fix.chatmode.md                  # Pinpoint root cause then apply a surgical fix
    └── refactor.chatmode.md             # Lean into small safe moves when improving design
```

## When to Use What

### Instructions (automatic, always-on for matching files)

You never invoke these. They activate based on the files in your context.

| Instruction | Applies To | Purpose |
|---|---|---|
| `copilot-instructions.md` | All chats | Identity, workflow, interaction style |
| `design.instructions.md` | All source files | Quality spectrum, simplicity, smells, SOLID one-liners |
| `refactoring.instructions.md` | All source files | Evolutionary design stance + safety |
| `xp.instructions.md` | Test files only | Testing principles |
| `java.instructions.md` | `*.java`, build files | Version detection, Effective Java, optional tooling |
| `kotlin.instructions.md` | `*.kt`, `*.kts` | Idiomatic Kotlin |
| `scala.instructions.md` | Scala sources, sbt | Scala 2/3 idioms |
| `python.instructions.md` | `*.py`, build files | Idiomatic Python, pytest, UV |
| `ruby.instructions.md` | `*.rb`, Gemfile | Idiomatic Ruby, Bundler |
| `typescript.instructions.md` | `*.ts`, `*.tsx` | Strict mode, types, testing detection |
| `spring.instructions.md` | Java/Kotlin + Spring config | DI, config, testing, web layer (rules-first) |
| `aws.instructions.md` | CDK / SAM / Serverless / `.aws/` config files | Read-only AWS CLI posture; identity & region confirmation; CloudWatch idioms; SSO expiry handling |
| `cdk.instructions.md` | `cdk.json`, CDK app/lib files | CDK v2; synth/diff via project wrappers; never `cdk deploy`; bootstrap verification; sandbox-aware |
| `astro.instructions.md` | Astro project files | Astro 5.x + TinaCMS conventions |

### Prompts (on-demand)

Use when you want to **think through** something with Copilot's help — you stay in control.

| Prompt | Invoke With | Use When |
|---|---|---|
| `/tdd` | `/tdd implement the discount calculator` | Red-green-refactor rhythm, you driving |
| `/review` | `/review` | Code review or PR diff review |
| `/refactor` | `/refactor this service class` | Apply named catalog moves (Fowler/Beck/Feathers) |
| `/expert-panel` | `/expert-panel should we use a Saga here?` | Multi-expert perspectives with citations |
| `/diagnose` | `/diagnose why is this test flaky` | Systematic debugging before fixes |
| `/challenge` | `/challenge I think we should use microservices` | Socratic stress-test of your assumptions |
| `/options` | `/options how should we handle pagination` | Trade-offs laid out before you decide |
| `/audience` | `/audience aws: learning` | Switch the explanation level for a stack mid-session |
| `/kit-doctor` | `/kit-doctor` | Audit this kit's token cost, bloat, schema drift, and coverage gaps |
| `/code-quality` | `/code-quality` | Offer to add opt-in lint / static-analysis / coverage tooling for this project's language |

### Chat modes (on-demand approach)

Pick a chat mode from the dropdown when you want to **lean into a particular rhythm** for the activity at hand. The mode is a suggestion, not a cage — the model retains every capability it had outside the mode.

| Chat mode | Lean into when |
|---|---|
| `build` | You're adding new behavior and want a TDD-flavored rhythm |
| `diagnose` | Sustained investigation; you expect more than a couple of turns of hunting before any code change. Read-leaning — no `editFiles` in its tools |
| `fix` | The symptom and likely cause are clear within a turn or two; you want surgical change with verification |
| `refactor` | You're improving existing code without changing behavior |

The `diagnose` ↔ `fix` pair is the deliberate split: `diagnose` keeps the model out of edit mode while you hunt the root cause; switch to `fix` once it's pinpointed. Both chat modes explicitly recommend the handoff so the model knows the other exists.

### Chat mode vs Prompt: Choosing

| Situation | Use | Why |
|---|---|---|
| "Build this feature for me" | `build` chat mode | Suggests TDD rhythm; you stay in the same conversation |
| "Let's build this together step by step" | `/tdd` prompt | Loads the explicit Red-Green-Refactor protocol on demand |
| "Fix this bug" | `fix` chat mode | Pinpoint then surgical change; good for short hunts |
| "Why does this happen?" / sustained hunt | `diagnose` chat mode | Read-leaning investigation; no edit tools |
| "Help me understand why this is broken" | `/diagnose` prompt | One-shot diagnosis script you drive |
| "Clean up this code" | `refactor` chat mode | Suggests small safe moves and tidy-first |
| "Should I refactor this?" | `/challenge` or `/options` prompt | Helps you decide before any action |

## Code Quality Tooling (Opt-In)

Invoke `/code-quality` when you want Copilot to offer adding linters / static analysis / coverage tooling to a project that doesn't have it. The prompt detects the language and proposes the relevant tools, configured as report-only — never build gates. (The opt-in catalog used to live inside each language instruction file; it was extracted to keep those files lean.)

| Language | Tools Prompted |
|---|---|
| Java | PMD, Checkstyle/Spotless, JaCoCo, SpotBugs |
| Python | ruff, mypy, pytest-cov |
| Ruby | RuboCop, SimpleCov, Sorbet |
| Kotlin | detekt, ktlint, Kover |
| Scala | scalafmt, scalafix, WartRemover, scoverage |

## Customize

1. Edit `.github/copilot-instructions.md` with your role, stack, values.
2. Delete language instructions you don't use.
3. Add instructions for your stack in `.github/instructions/`.
4. Add prompts for your recurring activities in `.github/prompts/`.

If you add, remove, or rename an instruction, **update this README**. It's the table of contents.

## Expanded Features Checklist

Tracking the ongoing restructure and planned additions.

### Done

- [x] Phase 1: Slim auto-loaded instructions. Moved Code Review, Expert Panel, and the Refactoring/Feathers catalogs out of always-loaded instructions and into `/review`, `/expert-panel`, `/refactor` prompts. Narrowed `xp.instructions.md` to test files only. Stripped cross-file "see also" preambles.
- [x] Phase 2: Compressed `spring.instructions.md` from ~4,600 tokens to ~1,430 tokens (rules-first; prose rationale removed).
- [x] Fixed broken code fence in this README and removed the embedded "Always Update README" instruction (it now lives where it belongs — at the bottom of the Customize section).
- [x] **AWS** instructions — Read-only by default; identity & region confirmation via `aws sts get-caller-identity` + region resolution chain; SSO expiry handling; CloudWatch logs idioms; sensitive-read guards (Secrets/SSM/IAM); per-env write policy (prod/staging never; dev/sandbox with explicit approval).
- [x] **AWS CDK** instructions — CDK v2; TS-primary, Python-secondary; `cdk synth`/`cdk diff` via project wrappers when present; never `cdk deploy`; bootstrap verification; context-based env selection; sandbox-aware narration.
- [x] **Audience hook** — `/audience <stack>: <level>` prompt + declared levels in `copilot-instructions.md`. Defaults to `working`; falls back to `learning` in detected sandbox accounts.
- [x] **Chat modes migration** — `agents/` → `chatmodes/` with the real Copilot schema (`tools:` arrays, no `handoffs:` fiction). Renamed `builder`/`fixer` → `build`/`fix` (verb-as-approach, not role-as-noun). Every chat mode opens with an explicit "additive, not limiting" Stance section and a "Stay capable" reminder so the model doesn't tunnel-vision. Stripped the unhonored `agent:` field from `tdd-cycle.prompt.md` and `diagnose.prompt.md`.
- [x] **`/kit-doctor` self-audit prompt** — measures token cost per file, computes steady-state context per file-type scenario, flags bloat / duplication / aspirational schema / structural bugs / coverage gaps. Read-only. Run it periodically to keep the kit honest without redoing the manual audit work.

### In progress / next

- [ ] **Stop-word removal pass** — a second-order compression sweep across the heaviest auto-loaded files. Strip low-information filler (weak verbs, redundant articles, "When you see X" → "If X", "Use ... always; ... never" → "... always; never ...") without touching code blocks, command syntax, negations, or already-terse rules. Run `/kit-doctor` first to pick the targets; expect 10–20% additional reduction on prose-heavy files. Watch for over-compression — past a point the model misreads cryptic text. Test by re-running `/kit-doctor` after.
- [ ] **Node.js / npm** instructions — version detection (`.nvmrc`, `engines`), ESM vs CJS, `package.json` scripts conventions, lockfile discipline, `npm audit` pragmatism, testing framework detection.
- [ ] `.vscode/mcp.json` template with sensible defaults (filesystem, GitHub) and commented examples (Postgres, Playwright, fetch). Worth including an AWS read-only MCP server that pairs with `aws.instructions.md`.
- [ ] `memory/` directory with an honest README: it's a convention, not a Copilot feature — a place to park repo-specific facts you want auto-loaded.
- [ ] `preferences/` directory: same honesty — workspace settings + an instruction file for personal style choices.

### Maybe / under consideration

- [ ] Go instructions.
- [ ] Rust instructions.
- [ ] Terraform instructions.
- [ ] A `/security-review` prompt covering OWASP top-10 patterns by language.
- [ ] A `/cost-aware` prompt that flags expensive patterns (N+1, full-table scans, missing indexes, unbounded loops).

## Philosophy

**Obedience school is for the owner, not the dog.**

The AI is capable. What's missing is a protocol for working together. These instructions teach you to give clear commands and teach the AI what you mean by them.

Core commands:
- **Sit**: Stop and clarify before generating code
- **Stay**: One task at a time
- **Heel**: Present options, let me decide
- **Speak**: State what you expect, then verify
- **Fetch**: Search and analyze context
- **Leave it**: Don't chase hallucinations

## License

MIT — Use it, modify it, share it.

---

By [Tim Andersen](https://timandersen.net) | [Blog Post](https://timandersen.net/post/copilot-custom-instructions)
