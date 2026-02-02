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

## What's Included

```
.github/
├── copilot-instructions.md         # Core instructions (always active)
├── agents/
│   ├── fixer.agent.md              # Debug mode: diagnose before fixing
│   ├── builder.agent.md            # TDD mode: test first, then implement
│   └── refactor.agent.md           # Cleanup mode: tidy first, behavior preserved
├── prompts/
│   ├── diagnose.prompt.md          # /diagnose - systematic debugging
│   ├── tdd-cycle.prompt.md         # /tdd - red-green-refactor
│   ├── challenge-me.prompt.md      # /challenge - Socratic questioning
│   └── options.prompt.md           # /options - present trade-offs
└── instructions/
    ├── typescript.instructions.md  # Rules for *.ts, *.tsx files
    └── astro.instructions.md       # Rules for Astro projects (customize or delete)
```

## Customize

1. Edit `.github/copilot-instructions.md` with your context (role, stack, values)
2. Delete or modify `astro.instructions.md` if you're not using Astro
3. Add path-specific instructions for your stack in `.github/instructions/`

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
