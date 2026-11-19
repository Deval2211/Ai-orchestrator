# 03 — Candidate Landscape and Reusable Patterns

> **Reconstructed on 2026-11-19** from the surviving candidate deep-dives and current T3 analysis. This is not a recovered original. It is a decision-oriented synthesis; project stars, model counts, and other volatile metrics are intentionally omitted. Current T3 findings supersede its older subsection in `parts/03`.

## 1. Evaluation question

Can an existing project supply the whole Hermes-first, cross-resource orchestration engine, or should the project reuse selected infrastructure and patterns while building a narrow resource-intelligence layer?

The consistent answer across the research is: **no inspected candidate covers the full requirement cleanly**. Reuse Hermes and native provider protocols, study proven orchestration patterns, and build the missing registry/router/execution-correlation layer without introducing another primary platform.

## 2. Candidate decision map

| Candidate | What it contributes | Decision for this project |
|---|---|---|
| **Hermes** | Primary conversation/memory/skills/tools, Kanban, delegation, workspaces, provider integrations | **Reuse as the primary system.** Not a replaceable dependency. |
| **FreeLLMAPI** | Model-axis gateway concepts, provider/model metadata, fallback and catalog lifecycle | **Optional integration candidate.** Revalidate health/contracts; do not make it the general orchestrator or copy its model catalog into a second registry. |
| **T3 Code** | Current source demonstrates provider instances, normalized adapters, durable orchestration commands/events, recovery, handoff, worktrees, diffs and PR workflows | **Use as a source-level reference.** It is not the smallest-capable cross-resource router and is not required as a dependency. |
| **herdr / herdr-core** | Durable terminal/PTY runtime, JSON-RPC, recovery, remote-machine possibilities | **Optional fallback adapter/reference** for tools without structured protocols. Prefer native CLI APIs over screen scraping. |
| **Multica** | Multi-runtime agent platform with a concrete Hermes-over-ACP implementation | **Reference only.** Its server/Postgres/web-platform shape and competing UI are heavier than the requirement. |
| **OpenClaw** | Complete gateway-centric agent platform | **Do not adopt for this purpose.** It would replace rather than extend the Hermes-first control plane. |
| **AionUi** | Agent UI and CLI integrations | **Do not adopt.** Hermes is the intended UI. |
| **CC Switch** | Provider/account configuration, usage and CLI-account management patterns | **Reference for account modeling.** Not a turnkey headless cross-resource orchestrator. |
| **LiteLLM and other model gateways** | Unified model APIs, budgets, load balancing, provider fallback | **Optional model-axis infrastructure only.** Justify operationally and verify exact license/version/security terms before adoption. |
| **OmniRoute / 9Router / WaLiAPI** | Historical research names for free-provider aggregation and fallback | **Leads, not established dependencies.** Identity, source, security, account-rotation terms, and current maintenance require fresh verification. |
| **OpenCode, Kiro, Pi, Cline, Claude Code** | Native coding-agent execution, sessions, workspace tools, structured protocols | **Candidate adapters.** Evaluate each version, account-isolation model, native protocol, privacy, and client/embedding terms independently. |
| **Ollama/local model servers** | Local inference and privacy/latency options | **Optional resources.** Include when hardware and measured model capability satisfy the task; do not assume a working local server exists. |
| **Browser profiles and browser AI services** | Access to services with no suitable API, using authenticated browser state | **Later phase only.** Dedicated profiles, manual authentication, sanctioned automation, no CAPTCHA/anti-bot bypass. |
| **MCP services** | Structured domain tools and resources accessible through a standard protocol | **Candidate resource type** with capability metadata and policy controls, not a universal agent-session mechanism. |

## 3. T3 Code patterns worth preserving

The current T3 analysis at [`13_T3_CODE_ORCHESTRATION.md`](13_T3_CODE_ORCHESTRATION.md) is the authoritative T3 source review for this corpus. It establishes these reusable ideas:

1. **Normalize providers behind adapters** without forcing every provider into identical behavior.
2. **Separate provider instance/account identity from the driver** so accounts can have independent auth, sessions, and state.
3. **Persist durable intent before side effects** and make commands idempotent.
4. **Use a durable event/outbox path** so restarts can reconcile rather than lose or duplicate effects.
5. **Recover explicitly** by classifying unknown provider process/session state instead of blindly replaying requests.
6. **Treat provider switching as a handoff** with curated context and artifacts, not imaginary native session portability.
7. **Keep worktrees, source control, diffs, and PR operations observable** through explicit workflows.
8. **Keep task-to-resource selection outside adapters** while provider protocol and account policy remain inside them.

T3 does not supply the required System 1 router across APIs, CLIs, accounts, local models, browser AI, and MCP services. Its value is orchestration mechanics, not the complete product.

## 4. Coding CLI landscape

The surviving CLI research in [`parts/08-coding-cli-landscape.md`](parts/08-coding-cli-landscape.md) compares Kiro, OpenCode, Pi, Cline, and Claude Code. The design lesson is not "wrap every CLI in a shell command." It is to select the best supported protocol per resource:

| Protocol | When to prefer it | Main risk |
|---|---|---|
| Native HTTP server / SDK | Available with structured sessions, events, cancellation, and errors | Shared server state can break account isolation; one isolated server may be needed per account. |
| ACP / JSON-RPC | Native agent protocol exposes sessions, prompts, permissions, and events | A working direct CLI does not prove that its ACP bridge works. Probe each adapter separately. |
| Structured subprocess output | Stream-json/NDJSON or equivalent gives machine-readable execution | Exit codes, event schema, cancellation, and resume behavior need conformance tests. |
| Interactive terminal / PTY | Last resort when no structured interface exists | Screen scraping is brittle and can hide state, prompts, and failures. |
| Browser automation | Only for a sanctioned browser-only service with no suitable API | UI drift, session state, privacy, anti-bot controls, and provider terms. |

Every adapter must prove its own authentication, isolation, structured output, native resume, cancellation, artifact, and recovery behavior. Sharing a CLI binary does not prove that two accounts are isolated.

## 5. Model gateways and free-resource aggregation

Gateways can be useful on the model axis, but they do not replace cross-resource task orchestration. The registry must preserve:

- provider instance/account identity;
- resolved upstream model identity behind aliases;
- declared versus observed limits and health;
- per-model and upstream privacy terms;
- client/embedding permission;
- source URLs and verification timestamps;
- tombstones for deprecated models and failed route evidence.

Free catalogs are volatile. [`14_FREE_RESOURCE_PRIMARY_SOURCE_REVIEW.md`](14_FREE_RESOURCE_PRIMARY_SOURCE_REVIEW.md) verifies 12 candidate coding/inference resources against official pages, and [`../../specs/resource-catalog.md`](../../specs/resource-catalog.md) condenses the registry implications. It confirms, among other things, that Mistral Free does not include API access and that free/client-only access cannot automatically be repackaged as a generic API.

## 6. Build, reuse, and integrate conclusion

The candidate evidence supports this division:

### Reuse from Hermes

- conversations, memory, skills, native tools and approvals;
- Kanban lifecycle, workspaces, comments, attachments, review/rework and recovery;
- delegation when a Hermes child is the desired execution unit;
- existing provider credential/fallback mechanisms where they apply to Hermes-owned calls.

### Build narrowly

- unified resource and provider-instance registry;
- task requirement and capability model;
- System 1 smallest-capable selection and explicit escalation;
- account health and isolation evidence;
- execution/attempt/event/artifact/verification correlation;
- portable handoff and recovery metadata.

### Integrate selectively

- direct hosted APIs and optional gateways;
- native coding CLI protocols;
- local model servers when measured suitable;
- MCP services;
- browser AI only under the later safety contract.

### Avoid

- a second primary UI or general agent platform;
- a second general Kanban/conversation system;
- universal PTY scraping when native protocols exist;
- mandatory dependency on an unverified gateway;
- provider-account rotation or client wrapping that violates terms.

This is the input to [`04_BUILD_VS_BUY_INTEGRATION_ANALYSIS.md`](04_BUILD_VS_BUY_INTEGRATION_ANALYSIS.md). Where that historical document conflicts with current requirements or newer source evidence, the canonical specs win.

## 7. Candidate due-diligence checklist

Before a candidate becomes an enabled dependency or resource, record:

- exact repository/package/version/commit and license text;
- official protocol and supported automation/client shape;
- credential storage and account/workspace isolation;
- native sessions, resume/fork, cancellation and recovery behavior;
- structured event, artifact, diff and verification support;
- privacy, training, retention, region and upstream terms;
- quota units, health signals and deprecation behavior;
- deployment footprint, security history and maintenance state;
- the smallest acceptance probe that proves the intended integration, not merely installation.

Unknown items stay unknown and block the relevant route class.

## 8. Evidence map

- [`parts/01-herdr.md`](parts/01-herdr.md) — terminal runtime, JSON-RPC, lifecycle and recovery.
- [`parts/02-multica-openclaw.md`](parts/02-multica-openclaw.md) — Multica Hermes/ACP implementation and OpenClaw platform analysis.
- [`parts/03-aionui-t3code-ccswitch.md`](parts/03-aionui-t3code-ccswitch.md) — historical AionUi/T3/CC Switch analysis; T3 corrections are in document `13`.
- [`parts/04-lowpromise-leads.md`](parts/04-lowpromise-leads.md) — unresolved or dismissed candidate leads.
- [`parts/05-api-gateway.md`](parts/05-api-gateway.md) — historical LiteLLM/gateway analysis and licensing concerns.
- [`parts/06-discovered-competitors.md`](parts/06-discovered-competitors.md) — broader candidate table.
- [`parts/08-coding-cli-landscape.md`](parts/08-coding-cli-landscape.md) — CLI protocols, sessions and account-isolation findings.
- [`13_T3_CODE_ORCHESTRATION.md`](13_T3_CODE_ORCHESTRATION.md) — current commit-pinned T3 source analysis.
- [`14_FREE_RESOURCE_PRIMARY_SOURCE_REVIEW.md`](14_FREE_RESOURCE_PRIMARY_SOURCE_REVIEW.md) — current primary-source resource review.

**Previous:** [`02 — Hermes and Current-State Audit`](02_HERMES_AND_CURRENT_STATE_AUDIT.md) · **Next:** [`04 — Build vs Buy vs Integrate`](04_BUILD_VS_BUY_INTEGRATION_ANALYSIS.md)
