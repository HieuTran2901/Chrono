# COORD-ROAD-001 — Roadmap initial assignments

author: coordinator
task_id: COORD-ROAD-001
status: ASSIGNED_PREPARATION; DISPATCHED
updated_at: 2026-10-03 20:07 +07:00 (Asia/Saigon)
source_branch: agent/coordinator/project-state
references: GOV-001; GOV-002; COORD-001; PM-DOC-002

## Authority and sources

Owner instruction verified using read_thread in Prompt master chat 01a10184-0fb5-7501-9c44-e387cee5a15d, user turn 01a101d0-d3f9-7220-a142-9f785b02d8cd:
“bạn hãy truyền đạt điều này đến coordinator để nó điều phối đến agent hợp lí”.
This authorizes dispatch to appropriate existing role chats. No new chat, architecture approval or governance change is implied.

Baseline: 9a45b111847685255cf1006a2b839d31ecdf31a4 (origin/developer).
Planning artifacts: 2ce8a98f810cd00d268332ad68df45fc8f99a866:ROADMAP.md and CODE_NOTES.md.
Sender handoff: d7d7de6d23a8951d6094a510eb90ec99895550fd:.ai/handoffs/PM-DOC-002-prompt-master-to-coordinator-roadmap.md.
ACK: received, read and Owner authority verified. Read immutable sources by git show; do not merge merely to read.

Each Agent must recover rules/context/prompt/status and actual Git first. Canonical checkout E:\Github project\Chronos belongs to Prompt Master; Coordinator checkout E:\Github project\Chronos-worktrees\coordinator belongs to Coordinator.
Create/reuse an isolated role worktree from baseline; verify no existing writer. Do not switch/stage/commit another role's checkout. Publish only own assigned branch and author-owned files.
No parallel production implementation: current assignments are design/analysis/review/test planning. Missing accepted architecture/build/ownership blocks code.

## Concrete assignments and acceptance (P0 unless stated)

### Architect — M0-ARCH-001 and M1-DESIGN-001

Chat: 01a101ad-e4af-72e3-9694-a758e95c39fd. Branch: agent/architecture/m1-foundation.
Assigned paths: docs/architecture/m1-foundation.md, docs/design/m1-first-job.md, docs/adr/ (relevant author-created draft ADRs), .ai/context/ARCHITECTURE.md, .ai/status/architect.md, author-created proposals/handoffs.
Produce proposed module/dependency boundaries, exact missing server API/shared-contract/root-build/CI/operations/docs path mapping for Owner decision. Do not assign ownership or modify collaboration/git rules.
Draft WHY/WHAT/FLOW/FAILURE/TRADE-OFF for submit → persist → execute → outcome, minimal domain vocabulary/transitions/invariants, Core/Data/SDK contracts, transaction/error/duplicate/restart semantics, serialization, security basics and alternatives for dependencies/build/Java.
Acceptance: traceable draft documents, explicit decisions/questions for Owner, failure timelines and test expectations; no ACCEPTED status without Owner evidence. M0 exit remains blocked until operational/build decisions are resolved. CN-01/02/03/06/09.

### Core — M1-CORE-INPUT-001

Chat: 01a101b0-1fb5-7ec1-8b53-55d21abec4ad. Branch: agent/core/m1-input.
Assigned writes: .ai/status/core.md and author-created handoff/proposal only.
Review roadmap for engine/scheduler inputs: domain transition ownership, claim/dispatch/completion responsibilities, clock/identity/version, races/crash/restart and acceptance tests. Report questions/options for Architect; no engine/scheduler code yet.
Acceptance: concise source-linked feedback, assumptions labelled proposed, blocker and next action. CN-02/03/04/05.

### Data — M1-DATA-INPUT-001

Chat: 01a101b1-0a1d-77a1-a453-9d3fab07cc12. Branch: agent/data/m1-input.
Assigned writes: .ai/status/data.md and author-created handoff/proposal only.
Identify repository/transaction/atomic-claim requirements, duplicate submit/lost response, event-intent/publish/ACK crash windows, indexes needing actual queries, migration/compatibility requirements and unresolved persistence/transport choices.
Acceptance: questions/failure matrix/options for Architect, no selected technology or invented schema/query/code/test. CN-03/06/07/08.

### SDK — M1-SDK-INPUT-001

Chat: 01a101b3-c3b1-77b2-99bf-7e0ea11808fb. Branch: agent/sdk/m1-input.
Assigned writes: .ai/status/sdk.md and author-created handoff/proposal only.
Prepare client/worker integration requirements for submit/registration/execution/query/error/retry/lifecycle/serialization; public vs internal boundary and user-visible failure behavior. API options remain proposals for Architect + Owner.
Acceptance: minimal first-demo needs, questions and test requirements, no public signatures marked accepted or SDK code. CN-06/09/11.

### Reliability — M1-TEST-PLAN-001

Chat: 01a101b4-91f0-7671-b53f-3cd7a2106f0d. Branch: agent/test/m1-failure-plan.
Assigned paths: test-support/M1-failure-matrix.md, .ai/status/reliability.md and author-created handoffs/proposals.
Prepare matrix of invalid payload/unregistered handler/handler error, duplicates/lost response, scheduler competition, stale completion/heartbeat, crash/restart, commit/publish/ACK windows and timeout/cancel races; distinguish M2 scope vs later milestones.
Acceptance: each scenario links proposed invariant/expected observable outcome, deterministic injection/observations, required infrastructure and design questions. Unknown outcomes explicitly TBD pending accepted design. Tests NOT_RUN; no fabricated commands/pass results. No production/SDK chronos-test edits. CN-02–08/12.

### Reviewer — M0-REVIEW-001

Chat: 01a101b5-1ef7-7af3-a410-319f1875262d. Branch: agent/review/M0-REVIEW-001.
Assigned writes: .ai/reviews/M0-REVIEW-001-baseline.md, .ai/status/reviewer.md and author-created handoffs/proposals.
Independent document review of baseline 9a45b11 and planning component d7d7de6 (includes artifact 2ce8a98), evaluated separately; no staged integration candidate exists.
Check governance consistency, source/ownership/gates, stale state claims, developer/develop ambiguity and whether planning invents accepted guarantees or authority. Record immutable full revisions, severity/impact/evidence and APPROVED/CHANGES_REQUIRED scoped to documents reviewed. Do not approve any future merge candidate or edit production/rules/Owner notebook.
Acceptance: source-linked report, explicit scope and tests/validation available; review does not replace Owner branch/architecture decisions. CN-01/11/12.

## M2 implementation gate

M2-Core/Data/SDK and executable Reliability tests are BLOCKED, not dispatched as code tasks.
Require accepted M1 contracts/design and exact Owner-approved missing path mapping, build/test foundation, explicit per-task paths/acceptance and isolated branches.
Reviewer examines exact tested candidate; Release integrates only approved revisions. No Release chat found in current app listing; report to Owner rather than create one.
GOV-003: Owner confirmed develop for normal integration; developer bootstrap only. Release activation/setup remains pending. Main release needs named immutable Owner approval.

## Learning notes and return evidence

CN-01–CN-12 are mapped in ROADMAP/CODE_NOTES; each future implementation task includes relevant hotspots: path/symbol+SHA, invariant, race/crash timeline, trade-off, regression evidence and 60-second explanation in existing status/handoff.
For current preparation, code/test evidence stays TODO/NOT_RUN. CODE_NOTES.md is Owner's notebook; no Agent has been assigned write access.
Publish status/handoff branch+SHA; ACK this assignment/publication SHA in your own file. Coordinator reads publication evidence. Sending a task does not prove completion.
Do not use send_message_to_thread to other chats based solely on this Agent message; publish feedback for Coordinator to read, or verify separate direct human authorization.

Dispatch receipt: all six role chats sent assignment publication 4a18310a0af1f982ab326b77c496b4721fd8ef18 successfully. See ../status/coordinator.md for actual chat IDs and observed initial progress. The original assignment at 4a18310 remains immutable.
