# Decision Log

**Convention:** approved requirements come from the user's current project vision. Proposed architecture choices remain subject to approval and verification; they are not presented as user decisions.

## 1. Approved requirements

| ID | Decision | Status | Clarification |
|---|---|---|---|
| D1 | Hermes remains the primary interface | Approved | Preserve conversations, memory, skills, native tools, profiles and workflows |
| D2 | A custom orchestration engine sits behind Hermes | Approved | It supplies resource intelligence and execution coordination, not another chat app |
| D3 | Task-based model/resource allocation | Approved | Select the smallest capable resource, subject to privacy and safety |
| D4 | Multiple API providers/accounts are supported | Approved | Each account has independent auth, health, limits and execution state |
| D5 | Multiple coding CLI integrations/accounts are supported | Approved | Isolation must be verified per CLI; blanket `HOME` separation is insufficient |
| D6 | Browser-based AI access is in scope | Approved, later phase | Dedicated profiles and CDP; only sanctioned automation; no CAPTCHA/quota evasion |
| D7 | Unified Hermes session continuity is required | Approved | One conversation can inspect/resume linked executions; no unsupported Hermes DB merging |
| D8 | Hermes skills and native workflows remain intact | Approved | Extend Hermes; pass only relevant approved context to external tools |
| D9 | Execution is local-first | Approved | Local control plane and durable records; cloud inference and optional local model servers are both allowed |
| D10 | Remote workers are future expansion | Approved scope | Not required for the initial local system |

## 2. Proposed design choices

These choices make the design concrete enough to review and prototype. They may be changed without re-opening D1–D10 when the underlying requirement is preserved.

| ID | Proposal | Rationale | Verification needed |
|---|---|---|---|
| P1 | Python 3.12+ with `asyncio` | Best Hermes/MCP fit for I/O-bound adapters | Prototype on supported runtime |
| P2 | SQLite with versioned SQL migrations and WAL | Local, durable, inspectable, no extra service | Migration/recovery tests |
| P3 | Native MCP plus a small Kanban worker lane | Supported interactive and long-running paths without forking Hermes | Tool discovery and lane contract smoke tests |
| P4 | Resource, provider-instance/account, execution and native-session IDs remain separate | Required for account isolation and honest continuity | Schema and recovery tests |
| P5 | Deterministic routing first, bounded model classification only for ambiguity | Lower cost/latency and easier evaluation | Labelled routing evaluation |
| P6 | FreeLLMAPI is an optional `llm_gateway` adapter, not the whole orchestrator | Reuse model-axis strengths without creating another control plane | Service inactive on 2026-11-19; restore and contract-test before selection |
| P7 | T3 Code is a reference, not a dependency | Keep Hermes UI and Python stack while reusing proven orchestration patterns | Commit-pinned source review completed |
| P8 | Execution rows/outbox before full event-sourced projections | Smallest design that provides idempotency and recovery | Load testing before adding projections |
| P9 | OpenCode is the first coding-CLI candidate | Structured interfaces and native sessions | Validate installed version, ACP/HTTP path and account isolation |
| P10 | Browser and local-model adapters follow the core API/CLI slices | Avoid making fragile or hardware-specific resources the foundation | One sanctioned vertical slice per resource type |

## 3. Change control

- Approved requirements change only with explicit user agreement.
- Proposed choices may change after review or failed verification; record the reason here.
- Historical research does not override current requirements or live system state.
- Volatile provider limits, models and free-tier terms require a new verification date, not a design-decision change.

See [`open-questions.md`](open-questions.md) for unresolved choices.
