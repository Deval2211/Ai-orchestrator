# Autonomy and Safety

**Status:** proposed safety, privacy, and verification policy

## 1. Autonomy model

The orchestrator may automatically classify tasks, select resources, retry transient failures, fall back to equivalent resources, escalate after insufficient results, and recover valid executions. Autonomy is bounded by capability, privacy, workspace, budget, concurrency, and approval policy.

No automatic path may bypass a trust boundary or treat a successful tool call as verified completion.

## 2. Trust classes

| Class | Examples | Default behavior |
|---|---|---|
| **A — bounded/read-only** | registry reads, side-effect-free health, summarization without tools | Allow when privacy and budget rules pass |
| **B — reversible workspace work** | edits in an isolated worktree, local tests, local artifact writes | Allow only inside declared workspace and resource permissions |
| **C — consequential/external** | package installation, git push/merge, deployment, external DB writes, deletion outside scratch, account/logout changes | Require explicit user approval |
| **D — prohibited** | quota farming, CAPTCHA bypass, anti-bot evasion, credential extraction, hidden secret logging, unsupported Hermes DB writes | Refuse |

Network access alone is not the risk category. An allowlisted inference request with non-sensitive content differs from publishing code or writing an external database. Data sensitivity and action consequence determine approval.

## 3. Approval contract

Before consequential execution, the plan shows:

- selected resource/account and destination;
- exact action and affected workspace/external system;
- data that will leave the machine;
- possible side effects and rollback limits;
- estimated cost if known, otherwise unknown;
- approval scope and expiry.

Approval is bound to the plan/action, not a blanket approval for future tasks. Background work without an available approval channel must remain within pre-approved classes or pause; it must not report an unanswered approval as granted.

## 4. Credential and account safety

1. Registry and plans contain secret references, never raw secrets.
2. Resolve credentials at execution time through the configured provider mechanism.
3. Construct each provider instance's environment explicitly and remove undeclared ambient credentials.
4. Scope credentials to the process/server/profile that needs them.
5. Redact before logs, artifacts, events, model handoffs, external reviews, and error reporting.
6. Stop existing account processes before logout or credential removal.
7. Do not treat a separate `HOME` as proof of isolation until credential/config/session paths are verified.
8. Do not read another provider's database for credentials or write to Hermes/FreeLLMAPI stores as a shortcut.

## 5. Privacy and data routing

Every candidate resource needs a freshness-stamped privacy record covering training, retention, logging, region, and confidentiality suitability.

| Content class | Routing rule |
|---|---|
| Public/non-sensitive | May use eligible free resources after capability and health filters |
| Internal/non-confidential | Requires provider terms compatible with internal use |
| Confidential/client code | Requires explicit suitable privacy evidence; unknown or training-enabled routes are blocked |
| Secrets/credentials | Never include in prompts or handoffs; use references and redaction |
| Local-only | Only local model/server or approved local processing resources |

Free does not imply private. Kilo Auto Free, NVIDIA trial endpoints, and other promotional routes may have logging or product-improvement terms. Their current official policy, not a historical table, determines eligibility.

## 6. Anti-abuse and account policy

The system may rotate after provider-signalled failures, cooldowns, expired authentication, or ordinary configured load balancing. It must not:

- create or farm accounts to evade free quotas;
- rotate to defeat billing/plan limits or anti-abuse controls;
- extract/share browser cookies or OAuth tokens across unauthorized clients;
- spoof another client or bypass an API restriction;
- solve CAPTCHA or disguise automation to evade service controls;
- treat multiple accounts as a way to multiply a provider-wide cap.

A quota exhausted state pauses the affected resource until a legitimate reset, upgrade, or user-selected alternative.

### 6.1 Client and embedding restrictions

Access terms apply to the integration shape, not only to accounts and content:

- OpenCode free chat models and Cline promotions must not be repackaged as unrestricted generic APIs when their official terms limit them to their own clients.
- Antigravity's consumer FAQ does not support wrapping `agy` inside third-party coding agents or automating account rotation. Use only a permitted direct scripting/API/enterprise path.
- A native CLI adapter is not automatically a sanctioned adapter. Record client/embedding permission and required transport with the resource's privacy and capability metadata.
- When terms are unclear, keep the resource disabled for that integration shape rather than inferring permission.

## 7. Browser safety

Browser AI is a scoped later capability:

- prefer official APIs and native CLI protocols;
- use dedicated manually authenticated profiles and separate processes;
- automate only services whose terms and product controls permit it;
- use Hermes browser/CDP tooling or a supported automation framework;
- mark login/CAPTCHA/manual verification as `authentication_required` and pause for the user;
- never auto-solve CAPTCHA, bypass anti-bot systems, or export profile secrets;
- record the service, profile, destination, and data classification in the plan.

Browser support is neither a blanket ban nor permission to bridge every consumer web UI.

## 8. Workspace safety

- Validate absolute workspace roots before dispatch.
- Use isolated worktrees/workspaces for parallel code mutation.
- Lock a workspace against unintended concurrent writers.
- Keep artifacts inside the designated artifact/workspace roots.
- Require approval for deletion or overwrite outside the authorized scope.
- Reconcile expected repository revision before native resume or handoff.
- Treat filesystem rollback and provider conversation rollback as separate operations; refuse uncoordinated automatic rollback.

## 9. Resource and execution safety

- Health checks must not trigger logins, billing, model calls, hooks, or workspace changes.
- Adapters own and clean up their process groups, servers, listeners, and temporary profiles.
- Idempotency prevents duplicate execution; uncertain side effects require review before replay.
- Global, provider, account, and workspace concurrency limits apply simultaneously.
- Retry, fallback, escalation, and handoff have independent attempt/time/spend caps.
- External review receives only authorized, bounded, redacted source snapshots.

## 10. Verification policy

Completion evidence is proportional to task risk:

| Task type | Minimum evidence |
|---|---|
| Structured extraction/classification | Schema validation and requirement check |
| Text generation | Output present, constraints checked, sensitive content reviewed |
| Read-only research | Sources and retrieval dates; unresolved conflicts stated |
| Code suggestion | Syntax/static checks where useful; no claim of applied behavior |
| Repository modification | Diff, relevant tests/lint/type checks, changed-file review, artifact hashes |
| External write/deploy | Test evidence plus explicit approval and post-write read-back |
| Browser action | Action result and destination verification, no hidden profile state assumption |

A process exit code of zero, a commit, or a model claiming “done” is insufficient alone. Failed verification triggers bounded rework or escalation; unverified areas are reported explicitly.

## 11. Recovery safety

Recovery must never blindly replay an operation with unknown side effects. Use:

- `retry` only for safe unaccepted/replayable work;
- `resume_native` only with valid provider state;
- `requeue` only for replay-safe durable effects;
- `handoff` after privacy/workspace revalidation;
- `needs_review` for uncertain mutations;
- `cancel` when no safe continuation exists.

Record the decision and evidence before new external work.

## 12. Incident behavior

On secret leakage, wrong-account use, unauthorized external writes, or uncontrolled processes:

1. stop new dispatch to the affected resource/account;
2. cancel owned running work where safe;
3. preserve redacted evidence without copying the secret again;
4. notify the user through Hermes with exact scope;
5. require manual re-enable after credential/policy correction;
6. add a regression test before restoring the adapter.

## 13. Acceptance criteria

- Confidential content cannot route to an unknown or unsuitable privacy policy.
- Consequential actions cannot execute without scoped approval.
- A duplicate dispatch cannot repeat an external write.
- A browser login/CAPTCHA pauses rather than evades.
- An account with exhausted legitimate quota cannot be multiplied through account farming.
- A failed verification cannot produce a successful completion.
- Every consequential external write is read back or has an explicit verification limitation.
