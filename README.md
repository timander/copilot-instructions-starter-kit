# Copilot Instructions Starter Kit

A ready-to-use template for GitHub Copilot custom instructions, agents, and prompts.

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

## Understanding Instructions, Prompts, and Agents

| Concept | What It Is | When It Activates | Think of It As |
|---------|-----------|-------------------|----------------|
| **Instructions** | Background rules injected into every conversation matching `applyTo` | Automatic — when you edit a matching file | Team coding standards posted on the wall |
| **Prompts** | Reusable conversation starters you invoke by name | On-demand — you type `/tdd` or `/diagnose` | A checklist you pull out for a specific activity |
| **Agents** | Autonomous personas with specific tools and capabilities | On-demand — you type `@builder` or `@fixer` | A specialist you call in to do a job |

**Instructions** shape *how* Copilot behaves. **Prompts** shape *what* Copilot asks. **Agents** shape *what* Copilot does.

## What's Included

```
.github/
├── copilot-instructions.md              # Always active — your identity, workflow, interaction style
│
├── instructions/                        # Automatic — activated by file type
│   ├── design.instructions.md           # Code quality, simplicity, SOLID, code smells, patterns
│   ├── xp.instructions.md              # TDD workflow, testing discipline
│   ├── refactoring.instructions.md      # Refactoring catalog, legacy code (Feathers)
│   ├── code-review.instructions.md      # Code review, PR review, expert panel mode
│   ├── java.instructions.md             # Java syntax, version detection, Effective Java
│   ├── typescript.instructions.md       # TypeScript strict, types, testing frameworks
│   ├── spring.instructions.md           # Spring wiring, config, testing philosophy
│   └── astro.instructions.md            # Astro/TinaCMS project-specific rules
│
├── agents/                              # On-demand — invoke with @agentname
│   ├── builder.agent.md                 # @builder — TDD feature building
│   ├── fixer.agent.md                   # @fixer — systematic debugging
│   └── refactor.agent.md               # @refactor — design improvement
│
└── prompts/                             # On-demand — invoke with /promptname
    ├── tdd-cycle.prompt.md              # /tdd — red-green-refactor cycle
    ├── diagnose.prompt.md               # /diagnose — systematic diagnosis
    ├── challenge-me.prompt.md           # /challenge — Socratic questioning
    └── options.prompt.md                # /options — present trade-offs
```

## When to Use What

### Instructions (automatic, always-on)

You never invoke these directly. They activate automatically when you edit matching files.

| Instruction | Applies To | What It Does |
|-------------|-----------|--------------|
| `copilot-instructions.md` | All conversations | Your identity, workflow, interaction style |
| `design.instructions.md` | All source files | Code quality spectrum, simplicity, SOLID, code smells, design patterns |
| `xp.instructions.md` | All source files | TDD workflow, call-your-shot discipline, testing principles |
| `refactoring.instructions.md` | All source files | Refactoring catalog, legacy code rescue (Feathers), evolutionary design |
| `code-review.instructions.md` | All source files | Code review mode, PR review mode, expert panel with cited sources |
| `java.instructions.md` | `*.java`, build files | Java version detection, JUnit/Mockito detection, Effective Java, Lombok |
| `typescript.instructions.md` | `*.ts`, `*.tsx` | Strict mode, types, null safety, testing framework detection |
| `spring.instructions.md` | `*.java`, Spring config | DI philosophy, wiring, configuration, Spring testing hierarchy |
| `astro.instructions.md` | `*.astro`, pages, components | Astro 5.x + TinaCMS project conventions |

### Agents (autonomous specialists)

Use agents when you want Copilot to **take action**: edit files, run tests, make changes.

| Agent | Invoke With | Use When |
|-------|-------------|----------|
| `@builder` | `@builder add user authentication` | Building a new feature with TDD. Writes tests, implements code, commits. |
| `@fixer` | `@fixer the login page returns 500` | Something is broken. Diagnoses systematically before fixing. |
| `@refactor` | `@refactor this service class` | Improving existing code. Ensures tests exist, then restructures safely. |

Agents can **hand off** to each other: `@builder` can hand off to `@refactor` for design review, and `@refactor` can hand off to `@builder` to add test coverage.

### Prompts (guided conversations)

Use prompts when you want to **think through** something with Copilot's help, without it taking autonomous action.

| Prompt | Invoke With | Use When |
|--------|-------------|----------|
| `/tdd` | `/tdd implement the discount calculator` | You want the red-green-refactor rhythm but want to stay in control of each step. |
| `/diagnose` | `/diagnose why is this test flaky` | You want to think through a problem systematically before jumping to solutions. |
| `/challenge` | `/challenge I think we should use microservices` | You want your assumptions stress-tested. Socratic questioning. |
| `/options` | `/options how should we handle pagination` | You need to make a decision and want trade-offs laid out clearly. |

### Agent vs Prompt: When to Choose Which

| Situation | Use | Why |
|-----------|-----|-----|
| "Build this feature for me" | `@builder` | Agent takes action autonomously |
| "Let's build this together step by step" | `/tdd` | Prompt guides the conversation, you drive |
| "Fix this bug" | `@fixer` | Agent investigates and applies the fix |
| "Help me understand why this is broken" | `/diagnose` | Prompt helps you think, doesn't jump to fixes |
| "Clean up this code" | `@refactor` | Agent makes the changes |
| "Should I refactor this?" | `/challenge` or `/options` | Prompt helps you decide before acting |

## Customize

1. Edit `.github/copilot-instructions.md` with your context (role, stack, values)
2. Delete language instructions you don't use (e.g., `astro.instructions.md`)
3. Add instructions for your stack in `.github/instructions/`
4. Modify agent tools and handoffs to match your workflow

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

MIT - Use it, modify it, share it.

---

By [Tim Andersen](https://timandersen.net) | [Blog Post](https://timandersen.net/post/copilot-custom-instructions)
