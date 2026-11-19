# Execution and Adapters

**Status:** proposed execution contract informed by inspected T3 Code source

## 1. Responsibilities

The execution coordinator turns an approved plan into a durable, correlated execution. It owns:

- idempotent intent and attempt records;
- adapter lifecycle and owned process cleanup;
- normalized events and native identifiers;
- artifacts and resource-level checkpoints;
- retry/fallback/escalation transitions;
- verification evidence and recovery classification.

Hermes Kanban owns the general task queue and review lifecycle. The orchestrator does not build a second general scheduler.

## 2. Adapter contract

All adapters expose the operations they actually support; unsupported operations are declared rather than emulated.

| Operation | Contract |
|---|---|
| `describe` | Static capabilities plus dynamic catalog metadata and provenance |
| `health` | Side-effect-free status; no model generation, login, hooks, billing, or workspace mutation |
| `open` | Create/attach native session state and validate account/workspace |
| `start` | Persist correlation and begin a native turn/operation |
| `stream` | Yield normalized events until terminal native state |
| `interrupt` | Cooperative stop with explicit force-stop behavior |
| `resume` | Resume native state only when supported and valid |
| `close` | Release owned processes, listeners, profiles, and temporary resources |

Adapters translate native failures into the router's error classes. They do not choose the next resource; the router does that using registry and verification evidence.

## 3. Capability declaration

Capabilities cover behavior that affects orchestration:

- streaming and incremental text;
- tool calls and approval requests;
- filesystem read/write and workspace roots;
- native session, thread, and turn identifiers;
- native resume, fork, rollback, and multi-thread-per-session;
- model switching in an existing session;
- provider handoff requirement;
- provider-native and application-owned subagents;
- structured output and vision;
- usage, cost, rate-limit reset, and quota metadata;
- background work that can outlive a turn.

A capability left enabled but failing at runtime is a bug. A missing capability triggers a documented fallback or rejection.

## 4. Initial adapter strategies

This table is a proposal, not a claim that every adapter is installed or verified.

| Resource | Preferred transport | Continuity | Isolation requirements | Initial status |
|---|---|---|---|---|
| Direct OpenAI-compatible API | HTTPS chat/completions or supported native API | Stateless; context bundle for a new call | Secret reference and provider privacy policy | First simple vertical-slice candidate |
| FreeLLMAPI | Supported local HTTP API | Gateway/model state plus orchestrator correlation | Read-only supported integration; no DB writes | Optional after fresh health/contract verification |
| Kilo Gateway | OpenAI-compatible gateway API | Dynamic server-side auto/free mapping | Confidentiality review and live catalog | Candidate; verified official documentation |
| OpenCode | Native HTTP/SDK or ACP; `run` JSON for bounded fallback | Native session ID when available | Per-account XDG/state map and owned server policy | First coding-CLI candidate; verify per installed version |
| Kiro | Headless structured output or ACP | Native resume identifier | Auth DB/config/session paths require real two-account proof | Later first-wave CLI |
| Other coding CLIs | Native structured protocol or ACP | Provider-specific | Must complete isolation matrix | Add individually, not as assumed support |
| Ollama/local server | Local OpenAI-compatible API | Stateless/model-managed | Local process and hardware capability measurement | Optional local-model resource |
| Browser | Hermes CDP/browser tooling or supported automation | Dedicated profile plus context bundle | Manual auth, dedicated process/profile, service policy | Later scoped phase |
| MCP service | Hermes/native MCP client or adapter call | Service-specific | Tool allowlist, secret scoping, trust policy | As required |

Do not use a durable PTY/screen-scraping runtime when a native structured protocol exists. A generic PTY adapter is a fallback for tools with no better interface.

## 5. Durable dispatch and idempotency

### 5.1 Intent before side effects

The coordinator persists the execution, first attempt, idempotency key, selected provider instance, workspace, and planned side effects before calling the resource. For asynchronous work, acknowledgement means **intent accepted**, not execution finished.

A small outbox is sufficient initially:

- `pending` — eligible for an owned worker;
- `running` — claimed with worker/lease and attempt count;
- `succeeded` — settled and verified;
- `retry_pending` — safe retry with deadline;
- `failed` — terminal with evidence;
- `cancelled` — no further side effects.

Do not copy T3's full event/projection system before read volume requires it. Preserve its transactional and recovery lessons.

### 5.2 Idempotency

- The caller provides a stable idempotency key for plan, execute, resume, and cancel.
- Repeating a key returns the stored receipt/result.
- Reusing a key for different input is a conflict, not a new execution.
- Provider-native request IDs are recorded when available but do not replace the orchestrator key.
- A retry after uncertain mutation requires review unless the provider operation is idempotent.

## 6. Normalized event contract

Every event includes execution ID, attempt ID, sequence, timestamp, provider instance, type, and redacted payload.

| Event family | Examples |
|---|---|
| Lifecycle | `execution.started`, `attempt.started`, `execution.completed`, `execution.failed`, `execution.cancelled` |
| Assistant output | `message.delta`, `message.completed`, bounded reasoning summary if available |
| Native correlation | `provider_session.updated`, `provider_thread.updated`, `provider_turn.updated` |
| Tools and approvals | `tool_call.started`, `tool_call.completed`, `approval.requested`, `approval.resolved` |
| Artifacts | `artifact.created`, `artifact.updated`, `artifact.verified` |
| Usage and health | `usage.updated`, `rate_limited`, `quota_exhausted`, `provider_unavailable` |
| Work coordination | `child.started`, `child.completed`, `handoff.created`, `verification.completed` |

Raw provider frames are diagnostic input, not an unlimited event-retention requirement. Redact and bound them before persistence.

## 7. Attempt and child lineage

Record separately:

- orchestrator execution and retry attempt;
- Hermes tool call and Kanban task/run;
- provider session/thread/turn;
- provider-native subagent;
- application-owned child execution;
- handoff source and destination.

A child can finish after the parent's provider turn. Completion delivery is correlated by child identity and persists independently of whichever parent attempt is active.

## 8. Cancellation and process ownership

1. Validate that the caller controls the execution.
2. Record cancellation intent once.
3. Ask the provider to stop through its native protocol.
4. Escalate owned process-group termination after a bounded grace period.
5. Release servers, MCP listeners, browser processes, and temporary profiles owned by the execution.
6. Mark externally uncertain work as `needs_review`, not automatically `cancelled` and safe.

Do not kill processes the adapter does not own. Do not leave orphan CLI, MCP, or browser children.

## 9. Workspace and checkpoint policy

- Read-only tasks may use an existing workspace without a worktree.
- Parallel or code-mutating tasks should use a Kanban-managed workspace or Git worktree.
- A per-workspace lock prevents two writers from sharing one tree unintentionally.
- Capture changed files, diffs, test output, and native resume state at meaningful milestones.
- A checkpoint is not a user-branch commit and not proof of correctness.
- Filesystem rollback and provider conversation rollback are separate capabilities. If they cannot be coordinated, refuse automatic rollback.

## 10. Recovery matrix

| Observed state | Recovery |
|---|---|
| Intent persisted, provider never called | Retry/requeue safely |
| Provider called but prompt acceptance unknown | Inspect native state; avoid duplicate mutation; review if uncertain |
| Provider accepted, native session resumable | `resume_native` with fresh health and workspace checks |
| Provider accepted, process lost, no native resume | Handoff with context bundle or cancel with explanation |
| Replayable read-only effect failed transiently | Bounded retry with same idempotency identity |
| Code-mutating effect outcome unknown | `needs_review`; do not blind replay |
| App-owned child is still valid | Let child finish and deliver completion to parent |
| Provider-native child process is gone | Close/cancel child state and report it |
| Verification failed | Bounded rework/escalation with prior evidence |

A stored `running` status does not prove provider acceptance. Recovery writes a new durable transition before performing new external work.

## 11. Verification

Adapter-level checks:

- native exit/completion status;
- output schema and acceptance criteria;
- declared tests/lint/type checks;
- changed-file and artifact integrity;
- unexpected external side effects;
- secret/redaction scan.

Hermes Kanban review handles semantic judgment and rework for consequential tasks. The two layers are complementary, not two bespoke verification engines.

## 12. Testing strategy

- Record real provider transcripts and replay them through adapters and normalization.
- Cover simple completion, multi-turn, queued turn, interrupt, approval, native resume, provider failure, process loss, usage limit, and child-outliving-parent cases.
- Use fake resources for timer, retry, idempotency, and recovery tests.
- Keep a small gated live smoke test per adapter; normal CI must not depend on free upstream availability.
- Assert that health checks do not create sessions or spend.
- Assert all persisted events and artifacts pass redaction and size limits.

## 13. T3 evidence

The following T3 patterns are directly relevant and source-inspected:

- provider instance as account/configuration boundary;
- capability-driven adapter behavior;
- transactional events/projections/receipts/outbox;
- post-commit effect workers;
- model/provider transition planning;
- app-owned versus provider-native children;
- recovery that rewinds unaccepted runs and retires process-bound effects;
- replay fixtures from real providers.

See [`../docs/research/13_T3_CODE_ORCHESTRATION.md`](../docs/research/13_T3_CODE_ORCHESTRATION.md). T3 itself is not an initial dependency.
