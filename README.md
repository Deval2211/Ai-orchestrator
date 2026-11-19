# AI Orchestrator

An intelligent, extensible resource-orchestration engine that turns **Hermes** into a unified control center for LLM APIs and gateways, coding agents and CLI accounts, local model servers, and sanctioned browser-based AI services—with task-based allocation and complete execution continuity.

> **Status:** documentation and design-review phase. No orchestrator implementation has been written.
> **Project path:** `/home/deval/Project/Ai-orchestrator`
> **Start here:** [`specs/design_draft.md`](specs/design_draft.md)
> **Documentation index:** [`docs/README.md`](docs/README.md)

## Vision

```text
You
  → Hermes: primary conversation, memory, skills, tools, approvals, Kanban
    → AI Orchestrator: resource intelligence + execution coordination
      → LLM APIs/gateways | coding CLIs | local model servers | browser AI | MCP
```

Hermes remains primary. The orchestrator does not replace its interface, memory, skills, sessions, or native skill-creation process.

## Core principles

1. **Smallest capable resource, not merely cheapest.** Capability, confidentiality and safety are hard constraints.
2. **Hermes stays primary.** Use MCP, Kanban and supported integration surfaces; never write directly to Hermes `state.db`.
3. **Unified resource pool.** Every provider instance/account has explicit authentication, privacy, capabilities, limits and observed health.
4. **Continuity without fiction.** One Hermes conversation can inspect and resume linked executions. Native CLI sessions stay native; cross-resource continuation uses explicit context bundles.
5. **Local-first control plane.** The orchestrator and its records are local. Cloud inference and optional local model servers are both valid resources.
6. **Autonomy with evidence.** Bounded retries/fallback/escalation, approval for consequential actions, isolated accounts/workspaces, recovery and verification before completion.
7. **No static free-tier mythology.** Model availability, limits and privacy terms are refreshed and dated.

## Proposed architecture

| Layer | Responsibility |
|---|---|
| Hermes | Conversation, memory, skills, approvals, native tools, durable task/review workflows |
| MCP + Kanban lane | Interactive and long-running orchestration surfaces |
| System 1 Router | Requirement extraction, policy/capability filters, smallest-capable selection and escalation |
| Registry | Resources, independent accounts, capabilities, privacy, catalog metadata and health |
| Execution coordinator | Adapters, normalized events, idempotency, artifacts, cancellation and resource checkpoints |
| Continuity layer | Hermes correlation, native resume, context bundles, handoff and recovery |
| Optional FreeLLMAPI adapter | Model-axis discovery/inference/fallback when restored and verified—not a second orchestrator |

The recommended initial stack is Python 3.12+, SQLite, native MCP, a small Kanban lane, and thin provider adapters. T3 Code is used as a source-inspected reference for adapter, event, account-isolation and recovery patterns, not as the runtime.

## Documentation map

### Canonical design

- [Complete design draft](specs/design_draft.md)
- [Architecture](specs/architecture.md)
- [System 1 Router](specs/system-1-router.md)
- [Resource Registry](specs/resource-registry.md)
- [Execution and Adapters](specs/execution-and-adapters.md)
- [Session Continuity](specs/session-continuity.md)
- [Autonomy and Safety](specs/autonomy-and-safety.md)
- [Candidate Resource Catalog](specs/resource-catalog.md)

### Decisions and delivery

- [Approved requirements and proposed choices](references/decisions.md)
- [Open questions](references/open-questions.md)
- [Documentation status and research guide](docs/README.md)

### Implementation phases

| Phase | Outcome |
|---|---|
| [1 — Foundation](phases/phase-1-foundation.md) | One Hermes → plan → execute → durable-record vertical slice |
| [2 — Intelligence](phases/phase-2-intelligence.md) | Explainable System 1 selection and bounded fallback/escalation |
| [3 — Execution](phases/phase-3-execution.md) | Coding CLI, account isolation, normalized events, Kanban/workspaces |
| [4 — Browser](phases/phase-4-browser.md) | One sanctioned manually authenticated browser workflow |
| [5 — Continuity](phases/phase-5-continuity.md) | Native resume, portable handoff, restart recovery and usage views |
| [6 — Advanced](phases/phase-6-advanced.md) | Parallel agents, evidence-based routing and optional remote workers |

### Research

- [Research brief and requirements](docs/research/01_RESEARCH_BRIEF_AND_REQUIREMENTS.md), [Hermes/current-state audit](docs/research/02_HERMES_AND_CURRENT_STATE_AUDIT.md), and [candidate landscape](docs/research/03_CANDIDATE_LANDSCAPE.md) — reconstructed series foundation; originals were never committed
- [T3 Code orchestration analysis](docs/research/13_T3_CODE_ORCHESTRATION.md) — commit-pinned, source-inspected on 2026-11-19
- [Free-resource primary-source review](docs/research/14_FREE_RESOURCE_PRIMARY_SOURCE_REVIEW.md) — detailed verification of 12 coding/inference resources
- [Source register](docs/research/11_SOURCE_REGISTER.md) — historical register with current updates
- [`docs/research/`](docs/research/) — earlier research retained for context; read the [status guide](docs/README.md#research-status) before relying on it
- [`multi-account-cli-isolation-analysis.md`](multi-account-cli-isolation-analysis.md) — earlier account-isolation analysis; verify against current CLI versions

## Repository layout

```text
Ai-orchestrator/
├── README.md
├── specs/                 # Canonical proposed system design
│   ├── design_draft.md
│   ├── architecture.md
│   ├── system-1-router.md
│   ├── resource-registry.md
│   ├── execution-and-adapters.md
│   ├── session-continuity.md
│   ├── autonomy-and-safety.md
│   ├── resource-catalog.md
│   └── undecided.md       # Pointer to the consolidated question list
├── phases/                # Six verified-vertical-slice delivery phases
├── references/            # Decisions and unresolved questions
└── docs/
    ├── README.md          # Documentation and evidence guide
    └── research/          # Dated source research and historical analysis
```

## Current verification notes

- T3 Code was inspected at commit `a6d2bed55b8ea1b4be27c9c77fdd65e0e7d479d5`; the analysis corrects older claims about account instances and PR support.
- FreeLLMAPI's service was inactive when checked on 2026-11-19. It remains optional until restored and contract-tested.
- OpenCode's official catalog showed free-model churn, and a direct DeepSeek free-model smoke test passed. Its ACP bridge was not used for the final research delegation because the bridge smoke test failed.
- No implementation, provider installation, or production deployment is implied by these documents.

## Next checkpoint

Review [`specs/design_draft.md`](specs/design_draft.md), the proposed choices in [`references/decisions.md`](references/decisions.md), and the open questions in [`references/open-questions.md`](references/open-questions.md). Implementation starts only after design approval.
