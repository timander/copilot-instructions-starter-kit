---
applyTo: "**/cdk.json,**/cdk.context.json,**/bin/*.ts,**/bin/*.py,**/lib/*-stack.ts,**/lib/*-stack.py,**/lib/**/*-construct.ts,**/lib/**/*-construct.py"
---

# AWS CDK

CDK v2. TypeScript-primary, Python sometimes. Auto-loaded with CDK files. Builds on `aws.instructions.md`: same identity/region/read-only stance.

## Stance

- **`cdk deploy` is human-only.** Never run it. Print the command if asked.
- **`cdk synth` and `cdk diff` go through the project's build tool when one exists.** Check `package.json` scripts, `Makefile`, `pyproject.toml`, `taskfile.yml` first. Wrappers encode profile/region/role-assumption/context flags the team already got right. Call `npx cdk` / `cdk` directly only when no wrapper exists.
- **No edits to `cdk.json` or `cdk.context.json` without explicit user approval.** Context drift causes CI surprises.

## Account / Environment Selection

Assumes the project uses **context flags** (`-c env=<name>`) backed by mappings in `cdk.json`:

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
2. Look up env in `cdk.json` context. If absent, **stop and ask** — don't guess.
3. Run AWS identity confirmation (see `aws.instructions.md`) with resolved profile + region.
4. Verify resolved Account matches env mapping. On mismatch — stop, surface the discrepancy.

If the project uses a different mechanism (`--profile` flag directly, env vars, wrapper scripts), adapt — state the mechanism out loud once at session start so the user can correct you.

## Bootstrap Verification

Before `cdk synth` or `cdk diff` in a new env this session:

- Check `CDKToolkit` stack exists in target account/region: `aws cloudformation describe-stacks --stack-name CDKToolkit --profile <p> --region <r>` (or via project wrapper).
- If missing or in `*_FAILED` state, **stop**. Bootstrap is `cdk bootstrap` — a write — human action. Print the recommended command and wait.

## `cdk synth`

- Prefer project wrapper (`npm run synth:dev`, `make synth-staging`, `task synth -- env=prod`).
- Direct invocation when no wrapper: `npx cdk synth -c env=<name> --profile <p>` (TS) or `cdk synth -c env=<name> --profile <p>` after `uv run`/`poetry run` (Python).
- Failing synth = code/config problem. Surface the error, propose smallest change to make synth pass; never auto-apply unless it's a one-token fix the user endorsed.

## `cdk diff`

- The read op that matters. Always run before discussing or approving a change.
- Output discipline: show resource-level changes (`Resources` section), highlight **destructive changes** (replacements, deletes) at top, summarize IAM/SecurityGroup changes separately — they bite.
- Multi-stack diffs: run per-stack, show one summary table: stack, additions, modifications, destructions.
- If `cdk diff` shows unintended drift, **stop and investigate** before suggesting further action. Drift is signal, not noise.

## Construct Style

- **L2 over L1** for everything common. Reach for `CfnXxx` only when L2 doesn't expose what you need.
- **L3 (patterns) sparingly** — hide too much for projects that need to evolve. OK for greenfield prototypes.
- **One stack per deployable unit.** A "stack" is a deploy boundary, not a "module."
- **Cross-stack references via explicit `Stack.of(...)` props**, not implicit imports. SSM Parameter Store OK for runtime parameter sharing; CloudFormation exports OK but lock stack topology.
- **Removal policies explicit** for stateful resources. `RemovalPolicy.RETAIN` for prod data stores; `DESTROY` only in dev/sandbox. Never silent.
- **Tags applied at stack level** with project's standard taxonomy (Environment, Owner, CostCenter, Project). If team has a `Tags.of(app).add(...)` helper, use it.

## TypeScript-Specific

- `strict: true` (from `typescript.instructions.md` — flagging because CDK projects often loosen it).
- Construct id is the **first** prop, props object is the **third**. Don't reorder.
- Prefer `Duration.seconds(n)` / `Duration.minutes(n)` over raw numbers for timeouts and TTLs.
- Use `Stack.of(this).account` and `Stack.of(this).region` over hardcoded values.

## Python-Specific

- CDK v2 imports: `aws_cdk` (not `aws_cdk.core` — that was v1).
- Use `from aws_cdk import aws_lambda as _lambda` to avoid shadowing the builtin.
- Stack construct ids are positional; props are keyword-only. Mind PEP 8 line length on long prop lists — break at commas.

## Testing

- Use `@aws-cdk/assertions` (`Template.fromStack(stack)`).
- Cover at minimum: resources created with expected types/counts; IAM policies don't grant `*` on `*`; destructive removal policies absent from prod-targeted stacks.
- Snapshot tests OK as a regression net; never the only test — they catch *change*, not *correctness*.

## Sandbox Note

If env is `sandbox`: still no `cdk deploy` from AI. Narrate the diff in one sentence ("Creates one Lambda, one log group, one SQS queue; no IAM changes"). Sandbox is where teammates learn — context helps, walls of text don't.

## Common Failure Modes (fast triage)

| Symptom | First check |
|---|---|
| `Need to perform AWS calls for account ...` | Bootstrap missing or profile wrong account |
| `No export named ...` | Cross-stack import name changed or stack not deployed |
| `Cannot find asset at ...` | Bundling failed silently — re-run synth with `-v` |
| `Resolution error: Supplied properties not correct` | Token used where a literal is required |
| Diff shows replacement of stateful resource | Logical id changed — usually unintentional, stop and check |
| `This CDK CLI is not compatible with the CDK library` | CLI vs lib version mismatch — print versions, suggest pinning |
