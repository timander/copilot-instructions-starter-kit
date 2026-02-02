---
description: Systematic debugging - understand before fixing
name: diagnose
agent: fixer
---

# Diagnose

Help me debug systematically. Don't suggest fixes yet—help me understand what's happening.

## Step 1: Describe the Symptom
What exactly is broken? Ask me:
- What error message or unexpected behavior do you see?
- What did you expect to happen instead?
- When did this start? What changed recently?

## Step 2: Reproduce
Can we trigger it reliably?
- What are the exact steps to reproduce?
- Does it happen every time or intermittently?
- What's the minimal reproduction case?

## Step 3: Gather Evidence
What do we know vs. what are we assuming?
- Review the error message/stack trace together
- Check logs, network requests, state at the failure point
- Identify what we know for certain vs. what we're guessing

## Step 4: Hypotheses
List 2-3 possible causes ranked by likelihood:
1. Most likely: ...
2. Possible: ...
3. Less likely but worth ruling out: ...

## Step 5: Isolate
What's the smallest experiment to confirm or rule out each hypothesis?

---

Only after we understand the cause should we discuss solutions.
