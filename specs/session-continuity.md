# Session Continuity

**Status:** proposed continuity and handoff contract

## 1. Requirement

Everything remains accessible and continuable from the same Hermes conversation. That does **not** require merging external CLI databases or pretending that all native sessions share one context.

Continuity means:

- the Hermes conversation references stable task and execution IDs;
- full redacted execution history and artifacts can be retrieved through Hermes tools;
- a later Hermes session can resume or hand off the work with the correct context;
- native CLIs may retain private sessions, but their state is correlated rather than assumed shared.

## 2. Ownership layers

| Layer | Owner | Contents |
|---|---|---|
| Conversation | Hermes | User prompts, tool calls/results, summaries and execution references |
| Durable task | Hermes Kanban | Card, dependencies, workspace, worker runs, comments, review/rework |
| Execution | Orchestrator | Plan, resource/account, attempts, events, errors, verification and lineage |
| Artifacts | Orchestrator | Redacted logs, diffs, tests, output files and context bundles |
| Native session | External resource | Provider-private session/thread/turn and execution state |

The orchestrator never writes directly to Hermes `state.db`. Hermes records MCP tool calls and Kanban activity through supported paths.

## 3. User-facing history model

```text
Hermes Main Session
├── Original prompt and constraints
├── Plan P1
│   ├── selected resource/account and explanation
│   └── privacy/approval decision
├── Execution E1 — Planning
│   ├── output, usage and verification
│   └── context bundle B1
├── Execution E2 — Backend / OpenCode account A
│   ├── Hermes Kanban task and workspace
│   ├── native session/thread references
│   ├── events, diff and tests
│   └── errors and recovery attempts
├── Execution E3 — Frontend / another resource
│   ├── handoff from E1/E2 through bundle B2
│   └── events and artifacts
└── Review and follow-up
    ├── reviewer verdict and requested changes
    └── linked rework execution
```

The transcript contains concise useful results and references. `get_execution` exposes detailed redacted history without forcing every frame into the model's context window.

## 4. Correlation contract

Every execution records available links:

```yaml
execution_id: exec_01...
plan_id: plan_01...
hermes:
  session_id: local-...
  tool_call_id: call-...       # interactive path, when present
  kanban_task_id: task-...     # durable path, when present
  kanban_run_id: run-...       # worker attempt, when present
resource:
  resource_id: opencode-console
  account_id: opencode:work
native:
  session_id: null
  thread_id: null
  turn_id: null
lineage:
  parent_execution_id: null
  handoff_bundle_id: null
```

`null` means absent or unsupported. Do not invent a native identifier when a provider does not expose one.

## 5. Context bundles

Cross-resource continuation uses an explicit portable bundle:

```yaml
schema_version: 1
objective: Implement and verify the requested change
acceptance_criteria: []
relevant_decisions: []
constraints:
  privacy: confidential
  allowed_tools: []
workspace:
  root: /absolute/workspace
  expected_revision: null
prior_work:
  summary: null
  failed_approaches: []
  verification_results: []
artifacts: []
open_questions: []
native_resume:
  supported: false
  references: {}
redaction:
  secrets_removed: true
  omitted_sensitive_material: []
```

### Bundle rules

1. Include only information required by the next resource.
2. Reference large artifacts rather than duplicating them.
3. Validate workspace identity and expected revision before use.
4. Preserve approvals and risk class; a new resource does not inherit broader permissions by default.
5. Redact before persistence and again before sending to the destination.
6. Record what was omitted and why.
7. Version the schema and keep the bundle linked to source and destination executions.

A bundle is a controlled handoff, not a complete lossless transcript.

## 6. Continuation paths

| Path | When valid | Behavior |
|---|---|---|
| Same Hermes conversation, no external resume | Follow-up question or new task | Hermes supplies relevant conversation context; orchestrator retrieves prior execution only as needed |
| Native resume | Same provider/account and supported live native state | Adapter resumes native session/thread and links the new attempt |
| Native read + new turn | Provider can load history but starts a new turn | Native history plus current objective and acceptance criteria |
| Portable handoff | Different resource/account or incompatible native history | New execution receives a validated context bundle |
| Requeue/retry | Prompt was not accepted or operation is safely replayable | Same idempotency lineage and bounded attempt |
| Human recovery | Side effects or workspace state uncertain | Show evidence and wait for instruction |

Cross-CLI “continue exactly where the other stopped” is not a valid promise. State what transferred and what was omitted.

## 7. Restart recovery

On startup, the orchestrator reconciles unfinished executions:

1. Read durable intent, attempts, native references, artifacts, and current provider state.
2. Determine whether the prompt/operation was accepted.
3. Revalidate provider account, workspace, privacy, and approval state.
4. Select `retry`, `resume_native`, `requeue`, `handoff`, `needs_review`, or `cancel`.
5. Persist the recovery decision before performing side effects.
6. Notify Hermes through the correlated tool/card and attach new evidence.

Hermes `delegate_task` children are process-local and are not treated as durable work. Use child Kanban cards when work must outlive the parent process or needs independent review.

## 8. Hermes skills and memory

The orchestrator must preserve:

- existing Hermes skills and native skill creation/update workflows;
- Hermes memory and conversation behavior;
- the user's selected interface and tools;
- profile isolation and credential policy.

When an external agent needs a relevant Hermes skill, pass an approved, bounded, compatible instruction/context bundle through its supported project-context mechanism. Do not copy the entire Hermes home, all memories, credentials, or chat history into an external tool.

## 9. Retention and privacy

- Store normalized events and useful artifacts, not unlimited raw provider streams.
- Apply size limits, redaction, and retention policy before persistence and before model handoff.
- Large artifacts remain addressable by ID/path and hash.
- Provider-native session deletion is a separate explicit action; removing an orchestrator record does not silently delete external history.
- Conversation compression does not delete the orchestrator's execution record.

Exact retention durations are an unresolved operator decision; until then, keep the design explicit rather than claiming indefinite raw-log preservation.

## 10. Acceptance criteria

- A user can reopen an old Hermes conversation and retrieve any linked execution's redacted events and artifacts.
- Native resume uses the correct account and native session reference.
- A different resource receives a portable context bundle with objective, decisions, workspace, verification, and open issues.
- A crashed execution is never blindly replayed after uncertain code mutation.
- External tools receive only approved relevant skills/context, not Hermes secrets or unrelated history.
- Review/rework and child completions remain linked after process restart and conversation compression.
