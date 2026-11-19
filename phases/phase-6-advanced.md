# Phase 6: Advanced Orchestration

**Status:** future expansion after Phases 1–5 are verified

## Goal

Coordinate independent workstreams, improve routing from real evidence, and add remote execution only when a demonstrated requirement justifies it.

## Work items

### 6.1 Parallel agents

- [ ] Decompose only tasks with independent acceptance criteria and workspace boundaries.
- [ ] Use Hermes Kanban for durable child cards/review and `delegate_task` for bounded short reasoning where appropriate.
- [ ] Track provider-native and application-owned child lifecycles separately.
- [ ] Enforce global, provider, account and workspace concurrency limits.
- [ ] Merge results and resolve conflicts through explicit review; never assume independent diffs compose safely.

### 6.2 Evidence-based routing

- [ ] Use execution outcomes, verification, latency, provider-reported usage and human overrides to refine local capability tiers.
- [ ] Add cost/latency-aware escalation only where measurements exist.
- [ ] Evaluate changes against the versioned routing set before rollout.
- [ ] Consider adaptive/bandit routing only when the evidence justifies its complexity.

### 6.3 Advanced verification

- [ ] Add multi-stage checks appropriate to task risk: static, tests, semantic review, UAT and external read-back.
- [ ] Support bounded automated rework with human approval for consequential outcomes.
- [ ] Measure false completion and unnecessary escalation rates.

### 6.4 Optional remote workers

- [ ] Define authenticated environment/worker identity and explicit filesystem/credential ownership.
- [ ] Dispatch tasks without moving secrets unnecessarily or confusing client and server workspaces.
- [ ] Support durable recovery, artifact transfer and remote cancellation.
- [ ] Add no fleet infrastructure until one real remote-worker use case works.

## Deliverable

A complex task is split into genuinely independent workstreams, executed within resource limits, reviewed as a coherent result, and completed with measurable verification. Remote execution remains optional.

## Acceptance criteria

- [ ] Independent child tasks run without workspace or credential leakage.
- [ ] Child completion is delivered by child identity, including after the parent's provider turn ends.
- [ ] Conflicting changes stop for review rather than auto-merging incorrectly.
- [ ] Routing changes improve the versioned evaluation set without privacy regressions.
- [ ] A remote worker, if added, proves environment-local filesystem/provider ownership and recovery.

## Explicitly deferred

Kubernetes/service fleets, multi-tenant SaaS controls, unbounded autonomous swarms and any infrastructure without a demonstrated need.
