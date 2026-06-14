# Copilot Instructions Starter Kit

A user-level GitHub Copilot CLI kit. Tight always-on context, deep content behind on-demand skills and agents, and a small CLI to activate and manage them across your workstation.

**Read the article:** [Disciplined AI, Harnessed](https://timandersen.net/post/copilot-token-optimized)
**Predecessor:** [Keep Your AI on a Leash](https://timandersen.net/post/copilot-custom-instructions) — describes the kit at [v1.0](https://github.com/timander/copilot-instructions-starter-kit/tree/v1.0)
**More writing:** [timandersen.net](https://timandersen.net)

---

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/timander/copilot-instructions-starter-kit/main/install.sh | bash
```

This clones the kit to `~/.copilot-kit`, sets up `~/.copilot/` symlinks, and writes `~/.copilot-kit/env` for your shell.

Add the env file to your shell profile so the env vars and `PATH` follow into every new shell:

```bash
echo 'source ~/.copilot-kit/env' >> ~/.zshrc   # or ~/.bashrc
exec $SHELL
```

Verify:

```bash
copilot-kit doctor
```

That's it. Now turn on what you want:

```bash
copilot-kit list                       # see what's available
copilot-kit activate diagnose          # diagnose agent
copilot-kit activate review            # review skill
```

### Updating

```bash
copilot-kit update    # git pull --ff-only inside ~/.copilot-kit
```

### Don't want to pipe-bash?

Clone first, then run the installer from the clone — `install.sh` detects you're inside the kit and uses that as the install location:

```bash
git clone https://github.com/timander/copilot-instructions-starter-kit ~/.copilot-kit
~/.copilot-kit/install.sh
```

### VS Code only (no CLI access)

If your workstation is locked down to VS Code Copilot, drop the kit's `.github/` folder into a project:

```bash
cd your-project
curl -L https://github.com/timander/copilot-instructions-starter-kit/archive/main.tar.gz \
  | tar -xz --strip-components=1 copilot-instructions-starter-kit-main/.github
```

This installs the per-repo VS-Code-flavored layout. No `copilot-kit` CLI, no workstation-level layered config — use it only when the user-level install isn't possible.

---

## Manage with `copilot-kit`

After installing, you have a small CLI to manage the kit without editing files by hand.

```bash
copilot-kit list                          # show all items with [active]/[inactive] markers
copilot-kit list agents                   # filter by type: agents, skills, instructions
copilot-kit activate <name>               # symlink an item so Copilot CLI sees it
copilot-kit deactivate <name>             # turn it off; source stays in the kit
copilot-kit discard <name>                # permanently delete from the kit
copilot-kit add <path> [--type=...]       # bring a new artifact in (your own rules, skills, etc.)
copilot-kit which <name>                  # show source and activation paths
copilot-kit doctor                        # verify install integrity
copilot-kit update                        # git pull --ff-only the kit
copilot-kit help                          # usage
```

Activation is non-destructive: source files always live in `~/.copilot-kit/`, and `~/.copilot/` carries symlinks to whatever you've turned on. `deactivate` removes the symlink. `discard` deletes the source.

### Worked example

```bash
# I work in Python and TypeScript; I never write Scala or Ruby
$ copilot-kit deactivate scala
$ copilot-kit deactivate ruby

# Adopt the review skill and diagnose agent for this week
$ copilot-kit activate review
$ copilot-kit activate diagnose

# Add a personal instruction file with my own preferences
$ copilot-kit add ~/Documents/my-style.instructions.md
✓ added instruction: my-style (active by default)

# Audit token cost
$ copilot-kit doctor
✓ COPILOT_CUSTOM_INSTRUCTIONS_DIRS includes /Users/me/.copilot-kit
✓ /Users/me/.copilot-kit/bin is on PATH
✓ /Users/me/.copilot/copilot-instructions.md → kit's copilot-instructions.md
✓ copilot CLI found (/usr/local/bin/copilot)
✓ all healthy
```

---

## Three Building Blocks

| Concept | What It Is | When It Activates | Think of It As |
|---|---|---|---|
| **Instructions** | Background rules injected when files matching `applyTo` are in context | Automatic | Team standards on the wall |
| **Skills** | Reusable how-tos invoked by name | On-demand — invoke `review`, `tdd`, `kit-doctor`, etc. | A checklist you pull out |
| **Agents** | A suggested approach for the conversation (CLI custom modes) | On-demand — picked from the agent picker | A rhythm you lean into, not a role you lock into |

**Instructions** shape *how* Copilot behaves. **Skills** shape *what* Copilot asks. **Agents** suggest *which approach* to lean into for the activity at hand — without giving up other capabilities.

### Agents are additive, not limiting

An agent is an approach the model leans into, not a persona that replaces its other skills. If you're in `build` and the conversation turns out to be a debugging session, you don't need to switch — the model can pivot. Each agent says this out loud in its body so the model doesn't tunnel-vision.

The trap to avoid when authoring new agents:

- ❌ "You are a TDD specialist" — role-as-noun. Sticks. Excludes everything else.
- ✅ "Lean into TDD when adding new behavior" — verb-as-approach. Pivotable.
- ❌ Long "Don't" lists — read as hard constraints.
- ✅ "Prefer X" / "Default to Y" — read as defaults.
- ❌ Narrow `tools:` arrays — read as "these are my only hands now."
- ✅ Generous (or absent) `tools:` arrays — preserve capability.

Every agent in this kit opens with a Stance section that names this principle.

---

## Token Discipline

GitHub Copilot's CLI pricing is metered. Every file that auto-loads costs tokens on every turn. This kit is deliberately structured so:

- `copilot-instructions.md` (always on) is small and stable.
- Auto-loaded instruction files contain **rules**, not rationale. Brief reasons only where they change behavior.
- Heavy content — refactoring catalogs, expert-panel discussions, full code reviews — sits behind **skills** that load only when invoked.
- `xp.instructions.md` is scoped to test files only; you don't pay for TDD doctrine when you're renaming a variable.

A typical Spring Java turn went from ~11,300 tokens of background context to ~5,200. A plain TypeScript turn is ~2,400.

Run `copilot-kit list` to see what you've activated and the `kit-doctor` skill to measure exactly where your tokens are going.

---

## What's Included

The kit's source layout (what you clone):

```
.
├── README.md
├── install.sh                            # First-time setup
├── bin/copilot-kit                       # Management CLI
├── copilot-instructions.md               # Always-on (symlinked to ~/.copilot/)
│
├── agents/                               # CLI custom agents
│   ├── build.agent.md                    # TDD rhythm for new behavior
│   ├── diagnose.agent.md                 # Sustained investigation (read-leaning)
│   ├── fix.agent.md                      # Pinpoint then surgical change
│   └── refactor.agent.md                 # Small safe moves on existing code
│
├── skills/                               # CLI skills (folder per skill)
│   ├── review/SKILL.md                   # Code review or PR walkthrough
│   ├── refactor/SKILL.md                 # Fowler + Beck + Feathers catalogs
│   ├── expert-panel/SKILL.md             # Multi-expert discussion
│   ├── tdd/SKILL.md                      # Explicit Red-Green-Refactor
│   ├── diagnose/SKILL.md                 # One-shot diagnosis script
│   ├── challenge/SKILL.md                # Socratic questioning
│   ├── options/SKILL.md                  # Trade-offs laid out
│   ├── code-quality/SKILL.md             # Opt-in tooling per language
│   ├── audience/SKILL.md                 # Switch explanation level
│   └── kit-doctor/SKILL.md               # Self-audit (token cost, drift, gaps)
│
└── .github/
    ├── copilot-instructions.md           # VS Code per-repo always-on (parallel to root file)
    ├── instructions/                     # *.instructions.md (loaded by CLI + VS Code)
    │   ├── design.instructions.md        # Quality spectrum, smells, simplicity
    │   ├── refactoring.instructions.md   # Evolutionary stance + safety
    │   ├── xp.instructions.md            # Testing principles (test files only)
    │   ├── java/kotlin/scala/python/ruby/typescript.instructions.md
    │   ├── spring.instructions.md        # DI, config, testing (rules-first)
    │   ├── aws.instructions.md           # Read-only AWS CLI posture
    │   ├── cdk.instructions.md           # CDK v2; no `cdk deploy` from AI
    │   └── astro.instructions.md         # Astro 5.x + TinaCMS
    ├── prompts/                          # VS Code per-repo prompts
    └── chatmodes/                        # VS Code per-repo chat modes
```

After install, the runtime layout looks like this:

```
~/.copilot-kit/                           # The kit (source of truth)
~/.copilot/                               # What Copilot CLI sees
├── copilot-instructions.md → ~/.copilot-kit/copilot-instructions.md
├── agents/<name>.agent.md  → ~/.copilot-kit/agents/<name>.agent.md    (per activation)
└── skills/<name>/          → ~/.copilot-kit/skills/<name>/            (per activation)

Plus: COPILOT_CUSTOM_INSTRUCTIONS_DIRS=~/.copilot-kit (via the env file)
```

---

## Instructions: auto-loaded when files match

| Instruction | Applies To | Purpose |
|---|---|---|
| `copilot-instructions.md` | Always | Identity, workflow, audience, interaction style |
| `design.instructions.md` | All source files | Quality spectrum, simplicity, smells, SOLID one-liners |
| `refactoring.instructions.md` | All source files | Evolutionary design stance + safety |
| `xp.instructions.md` | Test files only | Testing principles |
| `java.instructions.md` | `*.java`, build files | Version detection, Effective Java |
| `kotlin.instructions.md` | `*.kt`, `*.kts` | Idiomatic Kotlin |
| `scala.instructions.md` | Scala sources, sbt | Scala 2/3 idioms |
| `python.instructions.md` | `*.py`, build files | Idiomatic Python, pytest, UV |
| `ruby.instructions.md` | `*.rb`, Gemfile | Idiomatic Ruby, Bundler |
| `typescript.instructions.md` | `*.ts`, `*.tsx` | Strict mode, types, testing detection |
| `spring.instructions.md` | Java/Kotlin + Spring config | DI, config, testing, web layer |
| `aws.instructions.md` | CDK/SAM/Serverless files | Read-only AWS CLI; identity + region confirmation; CloudWatch idioms |
| `cdk.instructions.md` | `cdk.json`, CDK app/lib files | CDK v2; synth/diff via project wrappers; never `cdk deploy` |
| `astro.instructions.md` | Astro project files | Astro 5.x + TinaCMS conventions |

## Skills: on-demand

| Skill | When to invoke |
|---|---|
| `tdd` | You want the Red-Green-Refactor rhythm; you drive |
| `review` | Code review or PR diff walkthrough |
| `refactor` | Apply named catalog moves (Fowler/Beck/Feathers) |
| `expert-panel` | Multi-expert perspectives with citations |
| `diagnose` | Systematic debugging script before any fixes |
| `challenge` | Socratic stress-test of an assumption |
| `options` | Trade-offs laid out before you decide |
| `code-quality` | Offer to add opt-in lint/static-analysis/coverage tooling |
| `audience` | Switch the explanation level per stack (learning / working / expert) |
| `kit-doctor` | Self-audit: token cost, bloat, schema drift, coverage gaps |

## Agents: lean-in approaches

| Agent | Lean into when |
|---|---|
| `build` | You're adding new behavior and want a TDD-flavored rhythm |
| `diagnose` | Sustained investigation; expect more than a couple of turns of hunting before any code change. Read-leaning — no file-editing tools |
| `fix` | The symptom and likely cause are clear within a turn or two; you want surgical change with verification |
| `refactor` | You're improving existing code without changing behavior |

The `diagnose` ↔ `fix` pair is the deliberate split: `diagnose` keeps the model out of edit mode while you hunt the root cause; switch to `fix` once it's pinpointed. Both agents explicitly recommend the handoff in their bodies.

---

## Customize

Bring your own:

```bash
# An always-on personal rules file
copilot-kit add ~/Documents/my-rules.instructions.md

# A team-specific agent
copilot-kit add ./security-reviewer.agent.md

# A skill folder (must contain SKILL.md)
copilot-kit add ./skills/my-deploy-checklist
```

Delete what you'll never use:

```bash
copilot-kit discard scala
copilot-kit discard ruby
```

Whenever you add, remove, or rename an instruction in the kit, **update this README** if you also touched the tables above. It's the table of contents.

---

## A note on the names

There's a `kit-doctor` skill (the self-audit Copilot runs inside the chat) and a `copilot-kit doctor` command (a quick install-integrity check from the CLI). Same word, different layer: one inspects the kit's *content*; the other inspects whether your *install* is wired correctly. If you're looking at token cost or schema drift, use the skill. If `copilot-kit list` shows nothing or the env var isn't set, run the command.

---

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

The v2 evolution: **the right tool for the job, at the right level of context.** Lean instructions auto-load; heavy content sits behind skills; agents are approaches, not personas. AI as a sharper tool — never a sloppy vibe coder.

---

## Acknowledgements

The `expert-panel` skill names authors whose published work has shaped my thinking and my career. See [ACKNOWLEDGEMENTS.md](./ACKNOWLEDGEMENTS.md) for the full credit and a consent-friendly note for any of them who'd prefer a different framing.

---

## License

MIT — Use it, modify it, share it.

By [Tim Andersen](https://timandersen.net) | [Blog](https://timandersen.net/post/copilot-token-optimized)
