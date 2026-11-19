# Open Questions

**Status:** consolidated unresolved decisions as of 2026-11-19
**Canonical design:** [`../specs/design_draft.md`](../specs/design_draft.md)

The documentation is complete enough for design review. The following questions still require user approval, implementation evidence, or both. They must not be silently answered by an implementer.

## 1. Decisions for the user

| ID | Question | Current recommendation | Why it remains open |
|---|---|---|---|
| U1 | What is the final project name? | Keep **AI Orchestrator** as the working name | Cosmetic; does not block a prototype |
| U2 | Which resource should run ambiguous System 1 classifications? | Evaluate Kilo Auto Free, an OpenCode free model, and a restored FreeLLMAPI decision route; choose from measured accuracy/privacy | Provider churn and confidential-data constraints make a permanent model choice premature |
| U3 | Should FreeLLMAPI be restored as the primary model gateway? | Integrate optionally after health/contract checks; keep direct configured resources usable | Its service was inactive and its working tree heavily modified on 2026-11-19 |
| U4 | Which coding CLIs belong in the first supported wave after the first vertical slice? | OpenCode first, then Kiro; add others through the same adapter/capability contract | Installation, account isolation and native protocol behavior require per-tool proof |
| U5 | Which task classes require mandatory human review? | Require review for consequential external writes and uncertain mutations; calibrate repository tasks by risk | Completion policy affects autonomy and safety |
| U6 | What are the event/artifact retention periods? | Keep normalized events and useful artifacts; avoid indefinite raw provider frames | Storage, privacy and debugging needs need an operator policy |

## 2. Evidence to resolve during implementation

These are not reasons to keep redesigning on paper, but they prohibit claiming support before the evidence exists.

| ID | Evidence required | Acceptance signal |
|---|---|---|
| E1 | MCP transport and tool result behavior | Hermes discovers and calls the orchestrator tools; long output does not block continuity |
| E2 | Kanban lane contract | Worker completion/review/block handoffs and workspace correlation work end to end |
| E3 | OpenCode native protocol choice | ACP or HTTP/SDK path passes prompt, streaming, resume and cancellation tests on the installed version |
| E4 | Multi-account isolation | Two real accounts cannot observe each other's credentials, sessions, catalog or billing state |
| E5 | FreeLLMAPI capability access | Supported read path and schema versioning work without writes or secret exposure |
| E6 | Routing accuracy | Versioned task set meets an agreed threshold and handles confidential/failure cases correctly |
| E7 | Recovery semantics | Pre-/post-provider-acceptance crashes produce safe, tested recovery outcomes |
| E8 | Browser eligibility | One dedicated profile completes a sanctioned workflow and pauses correctly on manual verification |

## 3. Deliberately not open for redesign without new evidence

- Hermes remains the primary interface.
- The orchestrator covers heterogeneous resource types, not only LLM APIs.
- Smallest capable resource beats merely cheapest.
- Local model servers and browser resources remain in scope.
- Hermes `state.db` is not a direct write target.
- Python + SQLite + MCP/Kanban is the recommended minimal baseline, subject to design approval.
- T3 supplies patterns, not the initial runtime.

## 4. Review checkpoint

Before implementation, review and approve or amend:

1. [`../specs/design_draft.md`](../specs/design_draft.md)
2. the proposed choices in [`decisions.md`](decisions.md)
3. the phased acceptance criteria in [`../phases/`](../phases/)
4. U1–U6 above, except any explicitly deferred to a later phase.
