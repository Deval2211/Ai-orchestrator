# Documentation Guide

This project is in the design-review phase. There is no orchestrator implementation in the repository.

## 1. Read in this order

1. [`../README.md`](../README.md) — vision and documentation map
2. [`../specs/design_draft.md`](../specs/design_draft.md) — self-contained proposed technical specification
3. [`../references/decisions.md`](../references/decisions.md) — approved requirements versus proposed choices
4. [`../references/open-questions.md`](../references/open-questions.md) — unresolved decisions and evidence gates
5. [`../phases/`](../phases/) — implementation sequence and acceptance criteria

## 2. Canonical specifications

| Document | Owns |
|---|---|
| [`../specs/design_draft.md`](../specs/design_draft.md) | Complete problem, approach, interfaces, data flow, failure/security behavior, tests and rollout |
| [`../specs/architecture.md`](../specs/architecture.md) | Components, ownership, deployment, resource types and state boundaries |
| [`../specs/system-1-router.md`](../specs/system-1-router.md) | Requirement extraction, filtering, selection and escalation |
| [`../specs/resource-registry.md`](../specs/resource-registry.md) | Resource/account schema, catalog lifecycle, privacy and isolation |
| [`../specs/execution-and-adapters.md`](../specs/execution-and-adapters.md) | Adapter operations, events, idempotency, outbox, cancellation and recovery |
| [`../specs/session-continuity.md`](../specs/session-continuity.md) | Hermes correlation, context bundles, native resume and handoff |
| [`../specs/autonomy-and-safety.md`](../specs/autonomy-and-safety.md) | Trust classes, approvals, privacy, browser and verification |
| [`../specs/resource-catalog.md`](../specs/resource-catalog.md) | Dated candidate resources and official source links |

If older documents disagree, these canonical specifications and the user's current requirements take precedence.

## 3. Evidence conventions

| Label | Meaning |
|---|---|
| **Approved requirement** | Explicit user decision from the current project vision |
| **Proposal** | Recommended design awaiting approval or implementation verification |
| **Verified** | Checked against current local state, source code or an official primary source on the stated date |
| **Historical** | Evidence from an earlier research window; not a current runtime claim |
| **Unknown** | Not established; must not be silently converted to `true`, zero, unlimited or healthy |

Provider models, quotas, pricing and privacy terms always require a verification date. Numerical values from secondary research are not canonical unless confirmed by an official source.

## 4. Research status

### Series foundation — reconstructed `01`–`03`

The initial Git history began at `04`; no original `01`–`03` files were committed or later deleted. These entries were reconstructed on 2026-11-19 from the surviving requirements, deep-dives, source register, and current corrections:

1. [`research/01_RESEARCH_BRIEF_AND_REQUIREMENTS.md`](research/01_RESEARCH_BRIEF_AND_REQUIREMENTS.md) — brief, vocabulary, evidence policy, and series map.
2. [`research/02_HERMES_AND_CURRENT_STATE_AUDIT.md`](research/02_HERMES_AND_CURRENT_STATE_AUDIT.md) — Hermes ownership, supported integration surfaces, continuity, and dated environment findings.
3. [`research/03_CANDIDATE_LANDSCAPE.md`](research/03_CANDIDATE_LANDSCAPE.md) — candidate decisions, reusable patterns, and the input to build-vs-buy analysis.

They are explicitly marked as reconstructions, not recovered originals.

### Current source-inspected research

- [`research/13_T3_CODE_ORCHESTRATION.md`](research/13_T3_CODE_ORCHESTRATION.md) — T3 at commit `a6d2bed55b8ea1b4be27c9c77fdd65e0e7d479d5`, inspected 2026-11-19.
- [`research/14_FREE_RESOURCE_PRIMARY_SOURCE_REVIEW.md`](research/14_FREE_RESOURCE_PRIMARY_SOURCE_REVIEW.md) — detailed primary-source verification for 12 coding/inference resources.
- [`../specs/resource-catalog.md`](../specs/resource-catalog.md) — conservative candidate-resource catalog with official links and verification limits.

### Historical research

Unless a file carries a newer verification note, everything under `docs/research/04` through `docs/research/12` and `docs/research/parts/` is **historical research**, not canonical architecture. It may contain useful source links and implementation leads, but also superseded claims, including:

- excluding browser AI or local model servers contrary to the current vision;
- treating FreeLLMAPI as a verified running dependency;
- rebuilding model routing that a gateway could own;
- outdated CLI installation/capability assertions;
- outdated T3 account and diff/PR findings;
- deprecated model IDs and free-tier limits.

Do not delete this corpus: it preserves how earlier decisions were reached. Do not implement from it without checking the canonical specs and current sources.

## 5. Key current references

- T3 Code: <https://github.com/pingdotgg/t3code>
- Hermes documentation: <https://hermes-agent.nousresearch.com/docs/>
- Kilo free models: <https://kilo.ai/docs/getting-started/using-kilo-for-free>
- OpenCode models: <https://opencode.ai/docs/console/models>
- Complete candidate links: [`../specs/resource-catalog.md`](../specs/resource-catalog.md)

## 6. Documentation maintenance rules

1. Change canonical behavior in the owning spec, not in several independent copies.
2. Update [`../references/decisions.md`](../references/decisions.md) when a proposal is approved or rejected.
3. Keep [`../references/open-questions.md`](../references/open-questions.md) as the only unresolved-question list.
4. Add new external findings as dated research with commit/source links.
5. Never present unexecuted commands, tests, health checks or provider support as verified.
6. Do not add implementation files during the documentation phase.
