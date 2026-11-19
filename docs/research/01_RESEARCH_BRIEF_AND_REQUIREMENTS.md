# 01 — Research Brief and Requirements

> **Reconstructed on 2026-11-19.** Documents `01`–`03` were absent from the initial Git history; this is a source-linked reconstruction, not a recovered original. Current requirements and [`../../specs/design_draft.md`](../../specs/design_draft.md) take precedence over historical research.

## 1. Purpose

Build an intelligent, extensible resource-orchestration engine behind **Hermes**. Hermes remains the user's primary interface for conversation, memory, skills, native tools, approvals, and durable task history. The orchestrator supplies resource intelligence, execution correlation, account isolation, recovery metadata, and verification evidence across heterogeneous resources.

The project is not another conversational AI application and not merely an OpenAI-compatible model proxy.

```text
User
  → Hermes
  → AI Orchestrator
  → LLM APIs / gateways / coding CLIs / local model servers / browser AI / MCP services
```

## 2. Approved product requirements

| Area | Requirement |
|---|---|
| Primary interface | Extend Hermes; do not replace its UI, sessions, memory, skills, or native skill-creation workflow. |
| Resource pool | Register hosted models, gateways, coding CLIs, independent accounts, local model servers, sanctioned browser AI, and relevant MCP services. |
| Account isolation | Treat each provider instance/account as an independent resource with its own credentials, quota, health, sessions, configuration, and concurrency. |
| Task allocation | Select the **smallest capable resource**, not simply the cheapest or largest model. |
| Escalation | Retry safely, fall back within policy, and escalate after verified insufficiency or suitable failure classes. |
| Execution autonomy | Automate assignment, resource selection, bounded retries, and recovery; require approval for risky actions. |
| Continuity | Preserve inspectable task history, prompts, decisions, events, artifacts, verification, native session references, and portable handoffs. |
| Verification | Do not equate a completed provider response with a completed task; collect task-appropriate evidence. |
| Local execution | Local orchestration infrastructure is allowed. Local model servers such as Ollama remain optional resources when measured capable. |
| Browser AI | Keep browser-based AI in scope as a later, dedicated-profile, manually authenticated, sanctioned capability. |
| Remote workers | Future expansion only after the local single-host design is reliable. |
| Implementation | Documentation and design first; no orchestrator implementation is authorized by this research corpus. |

## 3. Non-goals and safety boundaries

- Do not create a second primary chat UI or duplicate Hermes memory/skills.
- Do not reduce the system to model-only load balancing; coding agents, accounts, workspaces, browser resources, and MCP capabilities are first-class.
- Do not write directly to Hermes `state.db` or treat internal persistence APIs as supported integration surfaces.
- Do not bypass CAPTCHAs, anti-bot controls, provider client restrictions, account limits, or paid quotas.
- Do not farm accounts, rotate identities to evade provider-wide limits, scrape credentials, or silently activate paid fallback.
- Do not claim cross-CLI native session sharing. Native resume and portable handoff are separate mechanisms.
- Do not hardcode volatile free-model catalogs, quotas, privacy terms, or regional availability as permanent facts.

## 4. Resource and routing vocabulary

| Term | Meaning |
|---|---|
| **Resource type** | A stable adapter family such as `llm_api`, `coding_cli`, `local_model_server`, `browser_ai`, or `mcp_service`. |
| **Provider instance / account** | The credential, configuration, session, and isolation boundary. Two accounts using one CLI are separate resources. |
| **Model** | A discoverable model exposed by a provider instance. Model identity is separate from account identity. |
| **System 1 router** | The policy and selection layer that maps task requirements to an eligible resource instance/account. |
| **Native resume** | Continuing a provider-owned session using its supported native mechanism and preserved state. |
| **Portable handoff** | Supplying a curated objective, history, artifacts, and state to a different resource without pretending it shares the old session. |
| **Free resource** | A point-in-time plan, model, credit, or promotion. Free does not imply unlimited, private, production-licensed, or permanently available. |

## 5. Task-allocation examples

| Task shape | Candidate resource class |
|---|---|
| Simple classification or extraction | Small, fast hosted or local model with the required schema reliability |
| Summarization | Economical model with adequate context and privacy terms |
| Code generation | Coding-capable API model or coding CLI, depending on workspace/tool needs |
| Complex planning | Strong reasoning model or specialized planning agent |
| Multi-file development | Coding CLI with native workspace tools, durable execution, and isolated account/workspace |
| Difficult debugging | Stronger model or specialized CLI after evidence-based escalation |
| Independent parallel work | Separate accounts/workspaces with explicit dependency and artifact contracts |
| Verification | Independent reviewer, deterministic test/lint/typecheck tools, or human review according to risk |

These are routing candidates, not static model assignments. Privacy, safety, capability, workspace requirements, and verification needs are hard filters before cost and availability optimization.

## 6. Research questions

The series answers these questions in order:

1. What does Hermes already provide, and which integration surfaces are supported? → [`02_HERMES_AND_CURRENT_STATE_AUDIT.md`](02_HERMES_AND_CURRENT_STATE_AUDIT.md)
2. What external projects and protocols can be reused or used as references? → [`03_CANDIDATE_LANDSCAPE.md`](03_CANDIDATE_LANDSCAPE.md)
3. What should be reused, integrated, extended, built, or dropped? → [`04_BUILD_VS_BUY_INTEGRATION_ANALYSIS.md`](04_BUILD_VS_BUY_INTEGRATION_ANALYSIS.md)
4. What architecture, stack, safety model, continuity model, and phased roadmap follow? → documents `05`–`10`
5. Which sources and unresolved evidence gaps support those conclusions? → documents `11`–`12`
6. What does current T3 Code source teach, and which free-resource claims survive primary-source checks? → documents `13`–`14`

## 7. Source and evidence policy

Evidence precedence:

1. Current user requirements and explicit corrections.
2. Current canonical specifications in [`../../specs/`](../../specs/).
3. Current source inspection and official provider documentation, with inspection dates and versions/commits.
4. Local runtime observations, labeled with model/account/time and without secrets.
5. Historical research and secondary sources as leads only.

Unknown values remain unknown. A successful smoke test proves only that route at that time. A provider catalog entry is not a service guarantee. Historical environment counts, star counts, model names, prices, quotas, and shutdown dates are never silently promoted into current configuration.

## 8. Series map

| Document | Role |
|---|---|
| `01` | This brief and requirement/evidence contract |
| `02` | Hermes and current-state audit |
| `03` | Candidate landscape and reusable patterns |
| `04` | Build-vs-buy-vs-integrate analysis |
| `05` | Historical recommended architecture |
| `06` | Historical technology stack and dependencies |
| `07` | Historical security and account management |
| `08` | Historical session continuity and recovery |
| `09` | Historical implementation roadmap |
| `10` | Historical open questions and architectural decisions |
| `11` | Source register and dated environment observations |
| `12` | Historical research-gap audit, including the FreeLLMAPI blind spot |
| `13` | Current commit-pinned T3 Code orchestration analysis |
| `14` | Current primary-source review of 12 coding/inference resources |

The [`parts/`](parts/) directory contains the underlying deep-dives. Canonical design lives in [`../../specs/`](../../specs/), and the phased implementation plan lives in [`../../phases/`](../../phases/).

## 9. Research-to-spec traceability

| Research output | Canonical destination |
|---|---|
| Hermes ownership and supported interfaces | [`../../specs/architecture.md`](../../specs/architecture.md), [`../../specs/session-continuity.md`](../../specs/session-continuity.md) |
| Heterogeneous resources and independent accounts | [`../../specs/resource-registry.md`](../../specs/resource-registry.md) |
| Smallest-capable routing and escalation | [`../../specs/system-1-router.md`](../../specs/system-1-router.md) |
| Adapter, event, recovery, and artifact patterns | [`../../specs/execution-and-adapters.md`](../../specs/execution-and-adapters.md) |
| Privacy, approval, client terms, and anti-abuse rules | [`../../specs/autonomy-and-safety.md`](../../specs/autonomy-and-safety.md) |
| Dated free-resource findings | [`../../specs/resource-catalog.md`](../../specs/resource-catalog.md) |
| Gated delivery sequence | [`../../phases/`](../../phases/) |
| Consolidated unresolved decisions | [`../../references/open-questions.md`](../../references/open-questions.md) |

**Next:** [`02 — Hermes and Current-State Audit`](02_HERMES_AND_CURRENT_STATE_AUDIT.md)
