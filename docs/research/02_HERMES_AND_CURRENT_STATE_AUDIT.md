# 02 — Hermes and Current-State Audit

> **Reconstructed on 2026-11-19** from [`parts/00-hermes-surface-audit.md`](parts/00-hermes-surface-audit.md), [`parts/07-hermes-integration.md`](parts/07-hermes-integration.md), [`parts/09-spec-audit.md`](parts/09-spec-audit.md), and current source checks. This is not a recovered original. Historical findings are labeled; canonical behavior is defined in [`../../specs/`](../../specs/).

## 1. Audit question

Which capabilities already exist in Hermes, which supported interfaces can connect an external orchestrator, and what remains genuinely missing? The central finding is that the orchestrator should not rebuild Hermes's general conversation or task infrastructure. Its distinct value is cross-resource intelligence and execution correlation.

## 2. Hermes ownership boundary

| Capability | Owner and integration implication |
|---|---|
| Conversations and session history | **Hermes.** Use supported session/tool workflows; never write directly to `state.db`. |
| Memory and skills | **Hermes.** External resources receive only relevant, authorized task context and compatible instructions. |
| Native tools and approvals | **Hermes.** Orchestrator policy must preserve approval and risk semantics rather than bypass them. |
| Durable task board | **Hermes Kanban.** Reuse task rows, workspaces, events, review/rework, comments, attachments, and recovery instead of creating a second general queue. |
| Parallel worktrees | **Hermes workspaces/worktrees**, with resource-specific isolation added only when required. |
| Subagent lifecycle | **Hermes delegation.** It selects where a Hermes child runs; it is not, by itself, an arbitrary CLI-execution adapter. |
| Provider credentials and fallback | **Hermes** for its own model calls. The orchestrator must still understand external CLI/account state that Hermes cannot see. |
| Resource intelligence | **Orchestrator.** Task requirements, heterogeneous capabilities, privacy/integration policy, account health, and smallest-capable selection. |
| Execution correlation | **Orchestrator.** Adapter attempts, native session IDs, normalized events, artifacts, verification evidence, and recovery metadata. |
| Model-axis gateway routing | **Optional gateway** such as FreeLLMAPI when healthy and contract-verified; not a second general orchestrator. |

## 3. Supported integration surfaces

| Surface | Fit for this project | Limit / rule |
|---|---|---|
| **MCP server called by Hermes** | Preferred initial language-neutral contract for planning, execution, execution lookup, and resource listing | MCP carries tool calls, not automatic Hermes history synchronization. Start with a small stdio surface. |
| **Hermes plugin tool** | Useful for in-session operations, policy hooks, and native correlation | Keep long-running work out of one blocked tool call. |
| **Kanban worker lane** | Preferred shape for durable long-running execution and reviewer/rework workflows | Workers terminate through supported Kanban operations; do not manipulate the board database directly. |
| **Delegation provider** | Useful when choosing a Hermes child model/profile is the actual requirement | A route is not a universal "run Kiro account 2" execution API. The current default backend has no per-task model parameter; it uses `delegation.provider` / `delegation.model` or inherits the parent. |
| **ACP** | Appropriate when an external system intentionally drives Hermes as an agent runtime | This is the opposite direction from Hermes calling its orchestrator; do not confuse the two. |
| **TUI gateway JSON-RPC / HTTP API** | External control of Hermes where documented | Not the primary orchestrator registration mechanism. |
| **Direct Hermes DB writes** | **Rejected** | Internal state is not a supported public integration contract. |

Proposed initial MCP methods are documented in [`../../specs/design_draft.md`](../../specs/design_draft.md): `plan_task`, `execute_task`, `get_execution`, and `list_resources`.

## 4. What Hermes cannot see without the orchestrator

- Whether a coding CLI account is authenticated, at a login screen, deprecated, or rate-limited.
- Whether two CLI configurations sharing one binary actually isolate credentials and sessions.
- Whether a resource can edit a workspace, resume natively, fork, stream structured events, or provide verification evidence.
- Whether a task needs a coding CLI rather than a larger text model.
- Whether a provider's privacy terms or client/embedding restrictions permit the intended route.
- Which native session, worktree, artifact set, and recovery point belong to a particular execution attempt.
- Whether a completed response satisfies the original task's acceptance criteria.

These gaps define the orchestrator's scope. They do not justify recreating Hermes conversations, Kanban, skills, or memory.

## 5. Continuity model

The research rejects two extremes: "external CLIs magically share sessions" and "the orchestrator needs its own competing conversation system." The supportable model has four correlated layers:

1. **Hermes conversation history** — the user's primary interaction record.
2. **Hermes Kanban state** — durable task lifecycle, workspace, review, comments, attachments, and handoffs.
3. **Orchestrator execution records** — plan, selected account, attempts, normalized events, native session references, artifacts, verification, and recovery metadata.
4. **Provider-native sessions** — private CLI/API/browser state, resumed only through supported native mechanisms.

A different resource receives a **portable handoff**, not a fabricated shared session. Unknown external side effects are reconciled before retry; durable intent must not cause unsafe replay.

See [`../../specs/session-continuity.md`](../../specs/session-continuity.md) for the canonical contract.

## 6. Current dated observations — 2026-11-19

These observations inform design but are not permanent environment guarantees:

- The NVIDIA NIM route `nvidia/nemotron-3.5-super-vl-120b-a12b` completed a direct smoke test successfully.
- OpenCode direct CLI inference succeeded with `opencode/deepseek-v4.1-flash-free`; the Hermes OpenCode Free ACP bridge did not pass its smoke test. A direct CLI success does not validate an ACP adapter.
- OpenCode's official catalog marked `GLM-5.3-Flash Free` deprecated on 2026-11-18, demonstrating why model discovery must be dynamic.
- FreeLLMAPI was inactive when checked, and its working tree contained unrelated uncommitted changes. Its earlier live counts and endpoints are historical until revalidated.
- T3 Code was inspected at commit `a6d2bed55b8ea1b4be27c9c77fdd65e0e7d479d5`; its source was analyzed without running its runtime or test suite.

## 7. Historical findings corrected by current requirements

| Historical claim | Current interpretation |
|---|---|
| Local AI inference is forbidden | Local orchestration is allowed, and local model servers remain optional resources. Capability and hardware must be measured. |
| Browser AI should be removed entirely | Browser AI remains a later sanctioned capability with dedicated profiles, manual login, and no anti-bot bypass. |
| FreeLLMAPI must be the foundation | It is optional model-axis infrastructure, not the general orchestrator. Direct configured resources must remain usable. |
| T3 lacks account isolation and diff/PR workflows | Current T3 source includes provider-instance account isolation and source-control/diff/PR workflows. See [`13_T3_CODE_ORCHESTRATION.md`](13_T3_CODE_ORCHESTRATION.md). |
| Delegation is an arbitrary execution backend | Delegation manages Hermes child routes. External CLI execution still needs a suitable adapter/worker contract. |

## 8. Safety and policy integration

- Approval enforcement must match the actual process boundary. A background service cannot assume an interactive Hermes approval prompt exists unless the integration provides one.
- Trust policy belongs to task/resource/account/workspace/risk, not merely to a global auto-approve flag.
- Privacy review covers prompts, code, tool logs, artifacts, retention, training, region, and upstream providers.
- Native CLI access is not automatically sanctioned automation. Record provider client/embedding restrictions; for example, Antigravity's consumer terms restrict third-party-agent wrapping and account rotation.
- Multiple accounts are for legitimate isolation and ownership, never quota evasion.

See [`../../specs/autonomy-and-safety.md`](../../specs/autonomy-and-safety.md).

## 9. Evidence still required before implementation

- Revalidate the exact Hermes plugin/MCP/Kanban contracts against the implementation version in use.
- Probe account isolation separately for each CLI; environment-variable assumptions are insufficient.
- Revalidate FreeLLMAPI health and contracts before making it an enabled resource provider.
- Verify each adapter's structured protocol, native resume semantics, cancellation, events, artifacts, and error taxonomy.
- Verify provider privacy and integration terms at registration and refresh time.
- Establish acceptance tests for routing quality and execution verification instead of treating model confidence as ground truth.

## 10. Evidence files

- [`parts/00-hermes-surface-audit.md`](parts/00-hermes-surface-audit.md) — Hermes capabilities and integration-surface ranking.
- [`parts/07-hermes-integration.md`](parts/07-hermes-integration.md) — detailed plugin, MCP, ACP, gateway, delegation, session, and approval findings.
- [`parts/09-spec-audit.md`](parts/09-spec-audit.md) — historical contradictions and missing decisions; several findings are intentionally superseded.
- [`12_RESEARCH_GAPS_AND_RECOMMENDED_SOLUTION.md`](12_RESEARCH_GAPS_AND_RECOMMENDED_SOLUTION.md) — historical FreeLLMAPI gap analysis and live observations from 2026-11-11.
- [`../../specs/architecture.md`](../../specs/architecture.md) — current ownership and deployment architecture.
- [`../../specs/session-continuity.md`](../../specs/session-continuity.md) — current continuity/recovery contract.

**Previous:** [`01 — Research Brief and Requirements`](01_RESEARCH_BRIEF_AND_REQUIREMENTS.md) · **Next:** [`03 — Candidate Landscape`](03_CANDIDATE_LANDSCAPE.md)
