# Roadmap

The ongoing restructure and planned additions for the Copilot Instructions Starter Kit. The README is the landing page; this file is the active work-tracker.

## Done

- [x] **Phase 1: Slim auto-loaded instructions.** Moved Code Review, Expert Panel, and the Refactoring/Feathers catalogs out of always-loaded instructions and into `/review`, `/expert-panel`, `/refactor` prompts (and matching CLI skills). Narrowed `xp.instructions.md` to test files only. Stripped cross-file "see also" preambles.
- [x] **Phase 2: Compressed `spring.instructions.md`** from ~4,600 tokens to ~1,430 tokens (rules-first; prose rationale removed).
- [x] **AWS** instructions — Read-only by default; identity & region confirmation via `aws sts get-caller-identity` + region resolution chain; SSO expiry handling; CloudWatch logs idioms; sensitive-read guards (Secrets/SSM/IAM); per-env write policy (prod/staging never; dev/sandbox with explicit approval).
- [x] **AWS CDK** instructions — CDK v2; TS-primary, Python-secondary; `cdk synth`/`cdk diff` via project wrappers when present; never `cdk deploy`; bootstrap verification; context-based env selection; sandbox-aware narration.
- [x] **Audience hook** — `/audience <stack>: <level>` skill + declared levels in `copilot-instructions.md`. Defaults to `working`; falls back to `learning` in detected sandbox accounts.
- [x] **Chat modes migration** — VS Code: `agents/` → `chatmodes/` with the real Copilot schema and additive-stance framing. CLI: native agents under `agents/*.agent.md` with the same framing.
- [x] **`/kit-doctor` self-audit skill** — measures token cost per file, computes steady-state context per file-type scenario, flags bloat / duplication / aspirational schema / structural bugs / coverage gaps. Read-only.
- [x] **Fable self-review pass** — tightened `code-quality`, `expert-panel`, `review`, `kit-doctor`, and `refactor`; aligned `copilot-instructions.md` with the additive-stance principle (dropped the `Don't` list); trimmed restated-description openers.
- [x] **`diagnose` agent** — sustained-investigation mode that pairs with `fix`. Read-leaning by tool config; uses "diagnose / pinpoint / root cause" vocabulary.
- [x] **GitHub Copilot CLI native layout** — `agents/*.agent.md` and `skills/<name>/SKILL.md` matching the CLI's expected file format. Skills replace VS-Code-only `*.prompt.md`; agents replace VS-Code-only `*.chatmode.md`.
- [x] **`install.sh` + `copilot-kit` management CLI** — workstation install with `list / activate / deactivate / discard / add / doctor / update / which / help`.

## In progress / next

- [ ] **Stop-word removal pass** — a second-order compression sweep across the heaviest auto-loaded files. Strip low-information filler (weak verbs, redundant articles, "When you see X" → "If X", "Use ... always; ... never" → "... always; never ...") without touching code blocks, command syntax, negations, or already-terse rules. Run `kit-doctor` first to pick the targets; expect 10–20% additional reduction on prose-heavy files. Watch for over-compression — past a point the model misreads cryptic text. Test by re-running `kit-doctor` after.
- [ ] **Node.js / npm** instructions — version detection (`.nvmrc`, `engines`), ESM vs CJS, `package.json` scripts conventions, lockfile discipline, `npm audit` pragmatism, testing framework detection.
- [ ] **MCP scaffolding** — `~/.copilot-kit/mcp/` directory with example configs (filesystem, GitHub, Postgres) plus a `copilot-kit mcp` subcommand to manage MCP server registration with Copilot CLI.
- [ ] **`memory/` directory** with an honest README: it's a convention, not a Copilot feature — a place to park repo-specific facts you want auto-loaded.
- [ ] **`preferences/` directory** — workspace settings + an instruction file for personal style choices.
- [ ] **Release tagging + version pinning** — `install.sh` should optionally pin to a release tag (`KIT_VERSION=v1.0.0 install.sh`) so users can lock to a known-good state.

## Maybe / under consideration

- [ ] **Go** instructions.
- [ ] **Rust** instructions.
- [ ] **Terraform** instructions.
- [ ] **`/security-review` skill** covering OWASP top-10 patterns by language.
- [ ] **`/cost-aware` skill** that flags expensive patterns (N+1, full-table scans, missing indexes, unbounded loops).
- [ ] **Per-project profile overlay** — `copilot-kit profile use <name>` to activate a saved set of agents/skills/instructions tied to a particular project context.
- [ ] **Drop VS Code parallel layout** if CLI becomes the dominant workflow — currently maintained in `.github/chatmodes/` and `.github/prompts/` for backward compatibility with the article's per-repo install.
