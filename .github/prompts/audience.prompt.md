---
description: Switch the explanation level for a stack mid-session
name: audience
---

# Audience Switch

Switch the explanation level for the named stack for the rest of this session.

## Usage

`/audience <stack>: <level>`

Examples:
- `/audience aws: learning` — I'm poking at AWS for the first time today; one-sentence narration before commands.
- `/audience cdk: expert` — Skip the hand-holding; run and report.
- `/audience aws: working` — Reset to the default.

## Levels

| Level | Behavior |
|---|---|
| `learning` | Precede non-trivial commands with one plain-English sentence. No tutorials. |
| `working` | Narrate only commands with non-obvious flags or risky semantics. |
| `expert` | Skip narration. Run and report. |

## After Switching

Acknowledge the switch in one line ("OK — `aws: learning` for the rest of this session"). Do not re-narrate prior commands; the switch is forward-looking only.
