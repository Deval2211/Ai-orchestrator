# Phase 1: Foundation

**Status:** planned; implementation not started

## Goal

Deliver one working vertical slice: Hermes can plan and execute a small read-only task through one configured resource, then retrieve its durable execution record.

## Preconditions

- Approve the baseline design and open decisions in [`../references/open-questions.md`](../references/open-questions.md).
- Choose one currently healthy direct API/gateway resource for the smoke path; do not hardcode a historical free model.
- FreeLLMAPI is optional until its service and contracts are freshly verified.

## Work items

### 1.1 Hermes integration

- [ ] Implement the minimal MCP surface: `plan_task`, `execute_task`, `get_execution`, `list_resources`.
- [ ] Start with stdio transport; verify discovery and calls from Hermes without modifying Hermes source.
- [ ] Preserve Hermes memory, skills, sessions and native tool behavior.
- [ ] Add correlation and idempotency inputs/outputs.

### 1.2 Registry and account model

- [ ] Validate resource, provider-instance/account, capability, privacy and observed-health schemas.
- [ ] Store declared configuration separately from observed runtime state.
- [ ] Support secret references and reject raw secret values.
- [ ] Discover the smoke resource's current model/catalog data instead of embedding a permanent free-model list.
- [ ] Include local-model and browser resource types in the schema even though their adapters come later.

### 1.3 Execution store

- [ ] Add versioned SQLite migrations and WAL configuration.
- [ ] Persist plans, executions, attempts, event index, artifacts and verification results.
- [ ] Implement idempotent receipts and the initial durable outbox.
- [ ] Apply redaction and event/artifact size limits at the adapter boundary.

### 1.4 One direct resource adapter

- [ ] Implement side-effect-free health and live capability discovery.
- [ ] Execute one bounded read-only prompt with normalized result/error events.
- [ ] Record provider-reported usage where available; leave unknown values unknown.
- [ ] Keep model/key fallback with the selected provider/gateway; do not build another model router.

## Deliverable

From Hermes, run a request such as “Summarize this public README in three bullets.” Hermes shows the explained plan, executes it after any required approval, and can retrieve the result and execution metadata afterward.

## Acceptance criteria

- [ ] Hermes discovers and calls every Phase 1 MCP tool.
- [ ] A real task completes through a currently available resource and appears in the Hermes tool history.
- [ ] SQLite contains the correlated plan/execution/attempt and normalized completion evidence.
- [ ] Repeating the execute idempotency key produces no second upstream request.
- [ ] Registry/config/artifacts contain no raw secret values.
- [ ] Disabling or deprecating the resource makes the next plan ineligible with a clear reason.
- [ ] Health checks produce no model call, login, billing, hook or workspace mutation.

## Explicitly deferred

Coding CLI writes, model-based classification, browser automation, parallel agents and remote workers.
