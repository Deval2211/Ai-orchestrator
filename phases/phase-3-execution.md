# Phase 3: Execution

**Status:** planned; implementation not started

## Goal

Execute real coding work through native CLI protocols with account isolation, normalized events, artifacts, cancellation and a durable Kanban path. Add local-model support only when an actual local server is needed.

## Work items

### 3.1 First coding CLI adapter

- [ ] Select the supported OpenCode HTTP/SDK or ACP path after testing the installed version.
- [ ] Implement open/start/stream/interrupt/resume/close with truthful capabilities.
- [ ] Capture native session/thread/turn references and changed-file artifacts.
- [ ] Use recorded native transcripts for normal regression tests and a gated live smoke test.
- [ ] Do not fall back to PTY scraping while a structured interface is available.

### 3.2 Account and process isolation

- [ ] Map credentials, config, sessions, caches and background-server ownership for the adapter.
- [ ] Construct explicit account environments and remove undeclared ambient credentials.
- [ ] Verify two real accounts cannot observe each other's state before enabling multi-account use.
- [ ] Terminate only owned process groups/servers/listeners.

### 3.3 Kanban lane and workspaces

- [ ] Add the small Hermes lane and required completion/review/block handoffs.
- [ ] Bind executions to Kanban task/run and workspace identifiers.
- [ ] Use per-workspace locks and Git worktrees for parallel code mutation.
- [ ] Persist intent/outbox before provider side effects and support duplicate-safe worker retries.

### 3.4 Additional resources

- [ ] Add Kiro only after its native protocol and two-account isolation checks pass.
- [ ] Add other CLIs individually through the same capability/event contract; installation is not claimed by documentation.
- [ ] Add an Ollama/local OpenAI-compatible adapter when a real local model server and suitable model are available.
- [ ] Add optional FreeLLMAPI integration after its service and supported read/inference contracts pass verification.

### 3.5 Verification

- [ ] Capture diff, changed files, test/lint/type-check output and artifact hashes.
- [ ] Separate native process completion from verification acceptance.
- [ ] Route semantic review/rework through Hermes Kanban.

## Deliverable

Hermes assigns a bounded repository fix to a coding CLI in an isolated workspace, displays progress, records the native session and artifacts, and sends the result through review.

## Acceptance criteria

- [ ] A real CLI task modifies only its authorized workspace and produces a diff plus verification evidence.
- [ ] Native session identifiers support resume where the provider advertises it.
- [ ] Cancellation leaves no owned child process or server behind.
- [ ] Duplicate Kanban dispatch does not repeat work.
- [ ] A reviewer can reject the result and trigger bounded linked rework.
- [ ] Two-account isolation is either demonstrated or the adapter remains explicitly single-account.
- [ ] Every advertised adapter capability has a fixture or live verification result.

## Explicitly deferred

A universal adapter for every CLI, remote workers, browser automation and adaptive multi-agent swarms.
