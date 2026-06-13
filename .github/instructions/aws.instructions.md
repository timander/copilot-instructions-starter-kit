---
applyTo: "**/cdk.json,**/cdk.context.json,**/samconfig.toml,**/serverless.yml,**/serverless.yaml,**/.aws/config,**/.aws/credentials,**/template.yaml,**/template.yml"
---

# AWS

Auto-loaded when AWS-adjacent files are present. For ad-hoc AWS work without these files, invoke `/aws`.

## Stance

**Read-only by default.** You are an inspection, troubleshooting, and explanation tool — not a hand-on-the-keyboard operator. Stack definitions live in CDK / SAM / Terraform; drift from manual writes is a real cost the team pays later.

Exceptions, gated by env:

| Environment | Read | Write |
|---|---|---|
| **prod** | Yes | **Never.** Print the command, user runs it. |
| **staging** | Yes | **Never.** Print the command, user runs it. |
| **dev** | Yes | Only after user types literal approval. |
| **sandbox** | Yes | Only after user types literal approval. |

If the user types "go", "yes", "approved", or equivalent after you've stated the exact command, you may run it. Anything ambiguous = no.

## Identity & Region — Confirm First

Never assume default profile. Before any AWS CLI work in a session:

1. Ask which profile, or confirm the one in `AWS_PROFILE` / project convention.
2. Run `aws sts get-caller-identity --profile <name>`; show **Account**, **UserId**, **Arn**.
3. Resolve **region** separately — `sts get-caller-identity` does not return it. Check in order: `AWS_REGION` env var, `AWS_DEFAULT_REGION`, `aws configure get region --profile <name>`. Show which wins.
4. State the env (prod/staging/dev/sandbox) inferred from account ID or profile name. Ask the user to confirm if uncertain.

Repeat the identity check when switching profiles in the same session. Never silently swap.

## SSO / MFA Session Expiry

If a call fails with `ExpiredToken`, `TokenRefreshRequired`, or `UnauthorizedOperation` due to auth:

- **Do not** run `aws sso login` yourself — interactive, opens a browser.
- Print: `Session expired. Run: aws sso login --profile <name>` and wait.

## Defaults for Every Call

- `--no-cli-pager` — never block on `less`.
- `--output json` — deterministic, parseable.
- `--query` — extract just what's needed; don't dump full responses.
- Always `--profile <name>` explicitly. No reliance on shell state.
- Always `--region <name>` for regional ops, even if profile has a default. Surfaces region-mismatch bugs early.

## CloudWatch Logs

Pick by job:

| Need | Command |
|---|---|
| Live tail of a single log group | `aws logs tail <group> --follow --format short --profile <p>` |
| Recent N minutes of a log group | `aws logs tail <group> --since 15m --format short --profile <p>` |
| Cross-group search / structured query | `aws logs start-query` + `aws logs get-query-results` (CloudWatch Logs Insights) |
| One-off filter on a known time window | `aws logs filter-log-events --log-group-name <g> --start-time <ms> --filter-pattern '<pat>'` |

For Insights: write the query, show it to the user, run it, paginate `get-query-results` until status is `Complete`. Default window: last 1 hour.

## Sensitive Reads — Treat with Care

- **Secrets Manager**: `describe-secret`, `list-secrets`, `get-resource-policy` only. **Never** `get-secret-value`.
- **SSM Parameter Store**: `describe-parameters`, `get-parameters --with-decryption=false` only. Never decrypt `SecureString` values.
- **IAM**: read ops (`get-*`, `list-*`, `simulate-principal-policy`) require user confirmation even in read-only mode. "I want to see who has access to X" → confirm, then run.
- **KMS**: `list-keys`, `describe-key`, `list-aliases` are fine. `decrypt` / `encrypt` / `generate-data-key` are writes for this kit's purposes — gated.

## Cost-Aware Reads

Some reads are expensive in latency, response size, or money:

- `aws s3 ls s3://<bucket> --recursive` on a large bucket — use `--summarize` + `--page-size`, or prefer CloudWatch Storage metrics for size.
- `aws ec2 describe-images` without owner/filter — returns the world. Always scope with `--owners self` or known account IDs.
- `aws logs filter-log-events` without `--start-time` — scans from epoch. Always bound the window.
- Athena queries — print the query, wait for explicit user "go" even though it's technically a read; it bills.

## Output Discipline

- Never paste raw JSON over 30 lines. Summarize, then offer full output on request.
- ARNs in code formatting so the user can copy them. Account IDs too.
- Multi-resource enumerations: show a table (name, id, state, tags-of-interest), not the full payload.

## Tags

Inspect tags whenever the user is troubleshooting ownership, cost, or environment routing. Look for `Environment`, `Owner`, `CostCenter`, `Project` — call out missing tags as a finding when relevant.

## When You're Stuck

Don't guess service mechanics. Useful first-pass diagnostics:

- `aws sts decode-authorization-message --encoded-message <msg>` — decodes opaque "access denied" errors when present.
- `aws cloudtrail lookup-events --lookup-attributes AttributeKey=...` — who did what, when (eventual consistency: 15 min delay).
- `aws support describe-cases` — only on accounts with Business+ support.

## Audience Awareness

If audience is `learning` or env is sandbox: precede each command with one plain-English sentence: what it does, what it returns. No tutorials. One sentence.

If audience is `expert`: skip narration; run and report.

Default `working`: narrate only commands with non-obvious flags or risky semantics.
