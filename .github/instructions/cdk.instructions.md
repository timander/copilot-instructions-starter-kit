---
applyTo: "**/cdk.json,**/cdk.context.json,**/bin/*.ts,**/bin/*.py,**/lib/*-stack.ts,**/lib/*-stack.py,**/lib/**/*-construct.ts,**/lib/**/*-construct.py"
---

# AWS CDK

CDK v2. TypeScript-primary; Python sometimes. Auto-loaded when CDK project files are present. Builds on `aws.instructions.md` — same identity / region / read-only stance applies.

## Stance

- **`cdk deploy` is human-only.** Never run it. Print the command if asked.
- **`cdk synth` and `cdk diff` go through the project's build tool when one exists.** Check `package.json` scripts, `Makefile`, `pyproject.toml`, or `taskfile.yml` first. Use those wrappers — they encode profile / region / role-assumption / context flags the team already got right. Only call `npx cdk` / `cdk` directly when no wrapper exists.
- **No edits to `cdk.json` or `cdk.context.json` without explicit user approval.** Context drift is a real source of CI surprises.

## Account / Environment Selection

This kit assumes the project uses **context flags** (`-c env=<name>`) backed by mappings in `cdk.json`:

```json
{
  "context": {
    "envs": {
      "dev":     { "account": "111111111111", "region": "us-east-1", "profile": "myco-dev" },
      "staging": { "account": "222222222222", "region": "us-east-1", "profile": "myco-staging" },
      "prod":    { "account": "333333333333", "region": "us-east-1", "profile": "myco-prod" },
      "sandbox": { "account": "444444444444", "region": "us-east-1", "profile": "myco-sandbox" }
    }
  }
}
```

Workflow:

1. Ask which env, or confirm from the user's prompt.
2. Look up the env in `cdk.json` context. If absent, **stop and ask** — don't guess.
3. Run AWS identity confirmation (see `aws.instructions.md`) with the resolved profile + region.
4. Verify the resolved Account matches the env mapping. If mismatch — stop, surface the discrepancy.

If the project uses a different mechanism (`--profile` flag directly, env vars, wrapper scripts), adapt — but state the mechanism out loud once at the start of the session so the user can correct you.

## Bootstrap Verification

Before `cdk synth` or `cdk diff` in a new env this session:

- Check that the `CDKToolkit` stack exists in the target account/region: `aws cloudformation describe-stacks --stack-name CDKToolkit --profile <p> --region <r>` (or via the project wrapper).
- If missing or stack is in `*_FAILED` state, **stop**. Bootstrap is `cdk bootstrap` — a write — and that's a human action. Print the recommended command and wait.

## `cdk synth`

- Prefer the project wrapper (`npm run synth:dev`, `make synth-staging`, `task synth -- env=prod`).
- Direct invocation when no wrapper: `npx cdk synth -c env=<name> --profile <p>` (TS) or `cdk synth -c env=<name> --profile <p>` after `uv run`/`poetry run` (Python).
- A failing synth is a code/config problem — surface the error, propose the smallest change to make synth pass, do not auto-apply unless it's a one-token fix the user already endorsed.

## `cdk diff`

- This is the read operation that matters. Always run it before discussing or approving a change.
- Output discipline: show resource-level changes (`Resources` section), highlight **destructive changes** (replacements, deletes) at the top, summarize IAM/SecurityGroup changes separately — they're the ones that bite.
- For multi-stack diffs, run per-stack and show one summary table: stack, additions, modifications, destructions.
- If `cdk diff` shows unintended drift (changes the user didn't expect), **stop and investigate** before suggesting any further action. Drift is the signal, not the noise.

## Construct Style

- **L2 over L1** for everything common. Reach for `CfnXxx` only when L2 doesn't expose what you need.
- **L3 (patterns) sparingly** — they hide too much for projects that need to evolve. Acceptable for greenfield prototypes.
- **One stack per deployable unit.** A "stack" is a deploy boundary; don't conflate it with a "module."
- **Cross-stack references via explicit `Stack.of(...)` props**, not implicit imports. SSM Parameter Store is acceptable for runtime parameter sharing; CloudFormation exports are acceptable but lock stack topology.
- **Removal policies are explicit** for stateful resources. `RemovalPolicy.RETAIN` for prod data stores; `DESTROY` only in dev/sandbox. Never silent.
- **Tags applied at the stack level** with the project's standard taxonomy (Environment, Owner, CostCenter, Project). If the team has a `Tags.of(app).add(...)` helper, use it.

## TypeScript-Specific

- `strict: true` (this comes from `typescript.instructions.md` — flagging because CDK projects often loosen it).
- Construct id is the **first** prop, props object is the **third**. Don't reorder.
- Prefer `Duration.seconds(n)` / `Duration.minutes(n)` over raw numbers for timeouts and TTLs.
- Use `Stack.of(this).account` and `Stack.of(this).region` over hardcoded values.

## Python-Specific

- CDK v2 imports are `aws_cdk` (not `aws_cdk.core` — that was v1).
- Use `from aws_cdk import aws_lambda as _lambda` to avoid shadowing the builtin.
- Stack construct ids are positional; props are keyword-only. Mind PEP 8 line length on long prop lists — break at commas.

## Testing

- Use `@aws-cdk/assertions` (`Template.fromStack(stack)`).
- Cover at least: resources are created with the expected types and counts; IAM policies don't grant `*` on `*`; destructive removal policies are absent from prod-targeted stacks.
- Snapshot tests are fine as a regression net but should not be the only test — they catch *change*, not *correctness*.

## Sandbox Note

If env is `sandbox`: still no `cdk deploy` from the AI. But narrate the diff in one sentence ("This will create one Lambda, one log group, and an SQS queue; no IAM changes"). Sandbox is where teammates learn — context helps, walls of text don't.

## Common Failure Modes (fast triage)

| Symptom | First check |
|---|---|
| `Need to perform AWS calls for account ...` | Bootstrap missing or profile wrong account |
| `No export named ...` | Cross-stack import name changed or stack not deployed |
| `Cannot find asset at ...` | Bundling failed silently — re-run synth with `-v` |
| `Resolution error: Supplied properties not correct` | Token used where a literal is required |
| Diff shows replacement of stateful resource | Logical id changed — usually unintentional, stop and check |
| `This CDK CLI is not compatible with the CDK library` | CLI vs lib version mismatch — print versions, suggest pinning |
