# Phase 5: Continuity and Recovery

**Status:** planned; implementation not started

## Goal

Make prior work inspectable and safely continuable from the original Hermes conversation across process restarts, native resume, cross-resource handoff and reviewer rework.

## Work items

### 5.1 Correlated history

- [ ] Complete Hermes session/tool-call/Kanban, execution/attempt, provider-instance and native-session links.
- [ ] Implement paginated `get_execution` views for events, artifacts, errors, usage and lineage.
- [ ] Keep concise Hermes results while preserving full redacted evidence in the orchestrator store.
- [ ] Verify access after Hermes conversation compression and process restart.

### 5.2 Context bundles and handoff

- [ ] Version the context-bundle schema and validate objective, decisions, workspace, artifacts, verification and open questions.
- [ ] Apply privacy/redaction checks at creation and destination.
- [ ] Link source and destination executions and record omitted context.
- [ ] Revalidate workspace revision and approval scope before handoff.

### 5.3 Recovery

- [ ] Reconcile unfinished outbox/execution records at startup.
- [ ] Distinguish prompt unaccepted, accepted, native-resumable, replay-safe and uncertain-mutation states.
- [ ] Implement `retry`, `resume_native`, `requeue`, `handoff`, `needs_review` and `cancel` outcomes.
- [ ] Persist each recovery decision before new side effects.

### 5.4 Verification and usage views

- [ ] Preserve verification/review outcomes across rework and handoff.
- [ ] Expose provider-reported usage/cost where available and unknown values honestly.
- [ ] Apply approved event/artifact retention limits without deleting necessary recovery state.

## Deliverable

Reopen an older Hermes conversation, inspect a previous coding execution, resume it natively or hand it to another eligible resource, and recover a deliberately interrupted task without duplicate mutations.

## Acceptance criteria

- [ ] Full redacted execution history and artifacts are retrievable from the original conversation.
- [ ] Same-resource continuation uses the correct native session/account.
- [ ] Cross-resource continuation uses a validated context bundle and records omissions.
- [ ] Crash tests before and after provider acceptance produce the documented safe recovery outcome.
- [ ] Uncertain code mutation is never blindly replayed.
- [ ] Review/rework and app-owned child completion survive process restart and conversation compression.

## Explicitly rejected

Direct writes to Hermes `state.db`, guaranteed lossless cross-CLI continuation, and blind “resume everything” behavior.
