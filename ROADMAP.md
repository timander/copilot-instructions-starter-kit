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
- [x] **Stop-word removal pass (Phase 1)** — applied to the three highest-value auto-loaded files (`copilot-instructions.md`, `aws.instructions.md`, `cdk.instructions.md`). Combined cut: ~5% (15,397 → 14,621 bytes). Honest finding: the kit was already largely rule-shaped from prior compression, so the original 10–20% prediction did not materialize. Remaining language files (java/kotlin/scala/python/ruby) sampled — also already rule-shaped, diminishing returns. See "Maybe" if a future pass surfaces a clearly bloated file.
- [x] **README v2 polish (2026-06-14)** — primary article reference updated to "Disciplined AI, Harnessed"; v1 article kept as `v1.0` backreference; install section consolidated to one primary `curl | bash` path with `copilot-kit doctor` as the verification step; clone-first and VS-Code-only alternates demoted to subsections; Philosophy section augmented with v2 evolution paragraph.
- [x] **ACKNOWLEDGEMENTS.md (2026-06-14)** — credit + consent-friendly recourse for the ten authors named in the `expert-panel` skill. Linked from README and from the skill itself.

## In progress / next

- [ ] **Tag `v2.0.0`** — Tim's task. Lands the restructured kit as a stable release so `KIT_VERSION` pinning has a real target and the v2 blog post's CTA references something durable.
- [ ] **Release tagging + version pinning in `install.sh`** — accept `KIT_VERSION=v2.0.0 ./install.sh` and pin the clone to that tag, so users can lock to a known-good state instead of always tracking `main`.
- [ ] **CI smoke test for `install.sh`** — GitHub Actions workflow that runs `install.sh` in a clean Docker container, sources the env, and verifies `copilot-kit doctor` exits 0. Catches install regressions before users hit them. Flagged in the v2 README simplification as the missing piece for "make sure install commands work properly."
- [ ] **Resolve `kit-doctor` skill vs `copilot-kit doctor` command collision** — same word, different layer (the skill inspects kit *content*; the command inspects *install integrity*). README has a "note on the names" callout but the friction remains. Options: rename the skill to `kit-audit`; rename the command to `copilot-kit verify`; or unify so `copilot-kit doctor` can also invoke the content audit.
- [ ] **Node.js / npm** instructions — version detection (`.nvmrc`, `engines`), ESM vs CJS, `package.json` scripts conventions, lockfile discipline, `npm audit` pragmatism, testing framework detection.
- [ ] **MCP scaffolding** — `~/.copilot-kit/mcp/` directory with example configs (filesystem, GitHub, Postgres) plus a `copilot-kit mcp` subcommand to manage MCP server registration with Copilot CLI.
- [ ] **`memory/` directory** with an honest README: it's a convention, not a Copilot feature — a place to park repo-specific facts you want auto-loaded.
- [ ] **`preferences/` directory** — workspace settings + an instruction file for personal style choices.

## Maybe / under consideration

- [ ] **CLI-first repo restructure** — promote `agents/`, `skills/`, top-level `copilot-instructions.md` as the canonical hierarchy; demote `.github/` to a `vscode-compat/` subfolder or drop entirely. Natural pre-v3-post milestone. Detailed proposal lives in personal memory.
- [ ] **`copilot-kit init <type> <name>`** — scaffolding command for new skills, agents, or instructions with the correct file shape and frontmatter already populated. Lowers friction for users adding custom content via `add`.
- [ ] **`copilot-kit search <keyword>`** — discoverability across activated and available items by purpose, not name. Useful as the kit grows past ~20 items.
- [ ] **`copilot-kit detect` + interactive auto-activation** — scan the current directory and recommend (or activate) the skills, instructions, and agents that match what's there. Two modes: `detect` for a one-shot recommendation, and an auto-mode that intercepts on context change (cwd switch, new project) and asks before flipping anything. Open: signal for "relevant" — file patterns, content sniffing, recent activation history?
- [ ] **Update-aware `doctor`** — `copilot-kit doctor` warns if `~/.copilot-kit` is behind `origin/main`. Soft nudge to `copilot-kit update`. Could also notify-once-per-day when `env` is sourced.
- [ ] **Pre-flight `activate`** — warn before activating items that overlap or conflict with already-active ones (e.g., two agents with overlapping `applyTo`-ish scope; two competing language instructions).
- [ ] **Token utilization dashboard** — `copilot-kit cost` (or `doctor --tokens`) shows what the current active set is loading on a typical turn and predicts the delta before you activate or deactivate something. Builds on the `kit-doctor` skill but user-facing and per-session, not just kit-wide.
- [ ] **Intelligent model picker** — detect which Copilot model fits the current task (long-reasoning vs. quick-edit vs. agent) and suggest or auto-set. Some of this depends on Copilot CLI capability — open questions: is context shareable across mid-session model switches? Can two models or two profiles share the same context window?
- [ ] **`skill-builder` skill (+ discoverability path)** — interrogative skill that asks for examples, edge cases, and tone preferences, then drafts a `SKILL.md`. Pairs with the `copilot-kit init` item above (init makes the file shape; skill-builder produces the content). Open question: how do user-built skills become discoverable to others — a community index, a separate registry repo, or a plugin architecture with a per-org override path?
- [ ] **Session-to-skill extraction** — `skill-extract` skill that reviews a long back-and-forth at end of session, identifies the corrections that mattered, and distills a reusable `SKILL.md` the user can save. A "what should I have explained differently" introspective pass. Captures hard-won prompt patterns without the user having to remember to do it.
- [ ] **Per-project profile overlay** — `copilot-kit profile use <name>` to activate a saved set of agents/skills/instructions tied to a particular project context. Should also support a workstation-default profile so the install lands with sane defaults, plus `copilot-kit profile save <name>` for capturing the current activation state without hand-editing config.
- [ ] **MCP setup pedagogy** — beyond the MCP scaffolding work above, document the *why / when / how* of adding an MCP server with worked examples. When does it pay off vs. when is it overkill? Which workflows benefit and which don't?
- [ ] **Copilot CLI feature integration guide** — as the CLI adds features (repo memories, hooks, custom agent modes), document what the kit cares about and what's a no-op for the kit. Helps users understand the boundary between "stuff the kit handles" and "stuff the CLI handles."
- [ ] **Stop-word pass on remaining language files** — `java/kotlin/scala/python/ruby` already sampled and found mostly rule-shaped. Likely <5% additional gain; deprioritized unless a future `kit-doctor` run surfaces a clearly bloated file.
- [ ] **Go** instructions.
- [ ] **Rust** instructions.
- [ ] **Terraform** instructions.
- [ ] **`/security-review` skill** covering OWASP top-10 patterns by language.
- [ ] **`/cost-aware` skill** that flags expensive patterns (N+1, full-table scans, missing indexes, unbounded loops).
- [ ] **Drop VS Code parallel layout** if CLI becomes the dominant workflow — currently maintained in `.github/chatmodes/` and `.github/prompts/` for backward compatibility with the article's per-repo install. (See "CLI-first repo restructure" above for the broader vision.)
