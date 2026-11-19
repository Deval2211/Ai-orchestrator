# Phase 2: Intelligence

**Status:** planned; implementation not started

## Goal

Add the System 1 router: explainable requirement extraction, capability/privacy filtering, smallest-capable selection, and bounded retry/fallback/escalation.

## Work items

### 2.1 Requirement extraction and evaluation

- [ ] Implement deterministic task-shape, capability, workspace and privacy extraction.
- [ ] Create a versioned evaluation set covering simple, reasoning, repository, confidential, browser and failure cases.
- [ ] Define local capability tiers and prove they order candidates rather than pretending to be universal benchmarks.
- [ ] Measure baseline routing accuracy and ambiguous-task rate.

### 2.2 Bounded model classification

- [ ] Add schema-validated classification only for tasks that are ambiguous under deterministic rules.
- [ ] Make classifier provider/model configurable and privacy-aware.
- [ ] Compare candidate free resources by accuracy, latency, availability and data-use terms.
- [ ] Fail closed or produce a conservative plan on malformed/unavailable classification.

### 2.3 Candidate selection

- [ ] Apply hard privacy, capability, health, workspace, budget and concurrency filters.
- [ ] Select the lowest sufficient tier, then task-class/privacy/reliability/cost preferences.
- [ ] Return selected account, model-route ownership, rejected candidates and reasons.
- [ ] Refresh stale catalogs and prevent retired models from being selected.

### 2.4 Failure policy

- [ ] Distinguish retry, equivalent fallback, capability escalation and context handoff.
- [ ] Add bounded attempts, backoff, per-task spend/time caps and provider/account concurrency.
- [ ] Honor provider reset metadata; never use account rotation to evade quotas.
- [ ] Record the full decision and escalation lineage.

## Deliverable

A raw task receives an explained plan and executes within its limits. Equivalent failures fall back; insufficient verified output escalates; confidential tasks never use an unsuitable resource.

## Acceptance criteria

- [ ] Simple classification uses the lowest sufficient healthy resource rather than a coding CLI.
- [ ] Complex repository work requires workspace/test capabilities.
- [ ] Confidential content is rejected from unknown or unsuitable privacy routes.
- [ ] A 429 causes cooldown/fallback, not an unnecessary stronger model.
- [ ] A failed verification causes bounded rework/escalation with preserved lineage.
- [ ] The evaluation set meets the approved routing threshold and has regression tests for privacy and deprecated models.
- [ ] The plan identifies who owns model/key routing inside the selected resource.

## Explicitly deferred

Adaptive/bandit learning, a large training pipeline, parallel-agent planning and automatic selection of unverified adapters.
