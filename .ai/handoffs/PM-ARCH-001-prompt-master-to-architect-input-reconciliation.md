# PM-ARCH-001 — Reconcile role inputs before Owner design decisions

author: prompt-master
recipient: architect
task_id: M1-DESIGN-001
status: READY_FOR_ARCHITECT_RECONCILIATION
updated_at: 2026-10-03 (Asia/Saigon)
source_branch: agent/prompt-master/roadmap-notes
references: ARCH-PROP-001; COORD-PUB-001; PM-PUB-001; GOV-001; GOV-003

## Owner instruction / scope

Direct Owner instruction in Prompt Master chat 01a10184-0fb5-7501-9c44-e387cee5a15d:
"bạn hãy truyền đạt đến Architect để tổng hợp các input trước khi trình quyết định thiết kế."

Reconcile actual published inputs before presenting design decisions. This request does not accept D1–D8, change ownership/Git authority, approve public API, or assign implementation. Coordinator retains task scheduling; Architect recommends design; Owner decides.

## Immutable input manifest

Read files with git show <sha>:<path>; fetch as necessary. Do not merge just to read inputs.
Publication receipt: f4eb0e3d1a97cf8f42900d6dc06ef3249897d0a8:.ai/handoffs/PM-PUB-001-prompt-master-to-coordinator-publication-receipt.md.
Coordinator manifest/state: 7834520200abb24738feb55a5923518c23c4fc51, .ai/handoffs/COORD-PUB-001-coordinator-to-prompt-master.md and Coordinator-owned context files.
Roadmap/notebook reference: 2ce8a98f810cd00d268332ad68df45fc8f99a866:ROADMAP.md and CODE_NOTES.md.

| Source | Published SHA | Required files |
|---|---|---|
| Architect draft | c33a045ba7ae2583a608698dc2b8b714ae2697fd | docs/architecture/m1-foundation.md; docs/design/m1-first-job.md; .ai/proposals/ARCH-PROP-001-m1-foundation.md |
| Core | da760dae97fbdc9d92c925f949e35eb3da95d6d8 | .ai/handoffs/CORE-M1-INPUT-001-core-to-architect.md |
| Data | e3d0027099fbdd7b82c9a9253d668018f3256bad | .ai/handoffs/M1-DATA-INPUT-001-data-to-architect.md |
| SDK | 12893a765a56526ea5630a83f8c24e54a7fc2122 | .ai/handoffs/SDK-M1-001-sdk-to-architect-contract-input.md |
| Reliability | edfe35d1ad464b967e12758298dcc786d4996f6a | test-support/M1-failure-matrix.md; .ai/handoffs/REL-M1-001-reliability-to-coordinator-architect.md |
| Reviewer | 6b693a820a8693b6d5dd414e6ede2a535c2e58ab | .ai/reviews/M0-REVIEW-001-baseline.md; .ai/handoffs/M0-REVIEW-001-reviewer-to-coordinator.md |
| Release | 18192bf2073fdef57a738b1013cc99de7bc2e6c6 | .ai/releases/2026-10-03-M0-foundation-plan.md |
| Coordinator | 7834520200abb24738feb55a5923518c23c4fc51 | .ai/context/CURRENT_STATE.md; .ai/context/TASK_BOARD.md; .ai/context/DECISIONS.md |

Read relevant author status alongside substantive inputs. Older statements that input branches are unpublished are historical: the receipt verifies all eight role refs. Publication does not imply accepted design or combined-candidate approval.

## Requested synthesis

1. ACK this handoff and its delivered SHA in your own status after recovering your isolated worktree and accepted rules.
2. Read the full role inputs and failure matrix. Map each material question, constraint and conflict to a design section or D1–D8; record resolved-by-draft, proposed resolution, needs clarification, or needs Owner choice. Link original SHA/path instead of copying all inputs.
3. Reconcile lifecycle/concurrency, lease/recovery/fencing, duplicate requests and external effects, persistence/response-loss boundaries, SDK wire/lifecycle/compatibility, security/limits and deterministic test oracles. Explain whether minimal recovery changes the M2/M3 milestone boundary; Coordinator replans only after approval.
4. Update existing Architect draft/proposal rather than creating a parallel source of truth. For each Owner decision, provide the problem, contributing inputs, agreement/conflict, recommendation, alternatives, affected roles/paths/API, benefits/risks, compatibility impact and required validation. Mark unanswered evidence/version assumptions explicitly.
5. Present a concise decision packet to Owner in your own chat only after this synthesis. Separate decisions ready for approval from blocking questions. Preserve PROPOSED_NOT_ACCEPTED until actual Owner evidence exists; do not create accepted ADRs by inference.
6. Publish only your own assigned documentation/status/handoff paths on your own branch. Record the new immutable revision and a file handoff for Coordinator to consume. Do not edit Coordinator state, other authors' files, CODE_NOTES, production code or active ownership/Git rules.

## Completion evidence / boundaries

A reader opening a fresh tab can trace every material input to its disposition, understand the remaining choices, and identify which decisions require Owner approval.
Report changed paths, published SHA, document checks and unresolved questions. Runtime tests remain NOT_RUN unless an executable target actually exists.
Existing Reviewer approvals cover their named baseline/planning revisions; no approval of the revised design or assembled candidate is implied.
This handoff grants no new cross-chat messaging authority, merge/deployment authority or architecture acceptance.
