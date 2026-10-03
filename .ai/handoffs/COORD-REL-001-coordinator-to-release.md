# COORD-REL-001 — Release operational foundation preparation

author: coordinator
task_id: M0-RELEASE-PLAN-001
recipient: release
status: ASSIGNED; DISPATCHED
updated_at: 2026-10-03 20:12 +07:00 (Asia/Saigon)
source_branch: agent/coordinator/project-state
references: GOV-001; GOV-002; GOV-003; M0-GOV-001; M0-REVIEW-001; M0-ARCH-001

## Recovery / authority

Release chat verified: 01a101de-81d0-7822-b019-5c4e2d0f90f5, host local, title RELEASE & INTEGRATION ENGINEER.
Owner directly assigned this role in that chat and informed Coordinator it now exists.
Owner's verified roadmap orchestration request remains authority for dispatch within existing ownership.
GOV-003: develop remains normal integration target; developer bootstrap only. No main/deployment authority.

Read Coordinator-published sources at efca8bc9c1758848bce9c7721f19adfdca651277 and this handoff's supplied publication SHA.
Baseline: 9a45b111847685255cf1006a2b839d31ecdf31a4.
Planning component assigned to Reviewer: d7d7de6d23a8951d6094a510eb90ec99895550fd (artifact 2ce8a98f810cd00d268332ad68df45fc8f99a866).
Do not substitute newer branch heads for these immutable components. New reports/changes require updated manifest and relevant re-review.
Canonical E:\Github project\Chronos belongs to Prompt Master; current observed HEAD 2de958e is a newer author revision, not an approved candidate.
Create/reuse isolated Release worktree under E:\Github project\Chronos-worktrees on agent/release/foundation-plan from baseline, after checking no other writer.

## Assigned work — P0

Assigned writes:
- .ai/releases/2026-10-03-M0-foundation-plan.md
- .ai/status/release.md
- Release-authored handoffs/proposals relevant to this task.

1. ACK task and GOV-003; verify origin, actual branches/worktrees/base revisions and remote reachability. Fetch/read messages by SHA, never merge only to read.
2. Prepare concrete develop setup plan under accepted Git authority. Verify whether origin/develop exists; preserve developer/bootstrap history. Record exact proposed base, steps and checks. Do not assume local remote-tracking absence proves current remote absence.
3. Prepare draft document-candidate manifest: baseline/planning/Coordinator revisions, purpose of each, review coverage/gaps, conflict/ownership analysis and validation requirements. No APPROVED claim or actual develop integration without exact candidate approval.
4. Identify any dependencies or missing evidence from M0-REVIEW-001/M0-ARCH-001. New candidate differs from Reviewer's currently assigned component review and needs exact candidate review before integration.
5. Specify required root build/CI/operations paths as requests pending Architect + Owner mapping/design. No build/CI edits or invented commands/versions.
6. Publish report/status on own branch, exact SHA and push outcome; unavailable network is UNVERIFIED. A local commit is not successful publication.

Acceptance: traceable plan/manifest with full known SHAs, actual Git checks/remote status, accepted authority and remaining blockers, validation/check commands with results only if actually run, next safe action.
Preparation only in this task: no develop/main push, no semantic conflict fixes, no production, deployment, tags/artifacts or root config.
Later integration/build assignment follows approved design/path mapping and exact tested/reviewed candidate. Release may stage isolated candidates under GOV-001, but this task requests a plan before staging.

CN-01: module/build/integration boundary explanation; code examples remain TODO. Put relevant evidence in Release status/report; do not edit CODE_NOTES.md.
Return via own published files/chat final; no tool message to Coordinator without direct human authorization. Coordinator reads evidence.
