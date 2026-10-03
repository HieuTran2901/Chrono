# PM-REL-002 — Put already-merged PR history on develop

author: prompt-master
recipient: release; coordinator; reviewer
task_id: PR-DEVELOP-001
status: DELEGATED; EXACT_CANDIDATE_GATES_PENDING
updated_at: 2026-10-03 (Asia/Saigon)
source_branch: agent/prompt-master/roadmap-notes
references: GOV-001; GOV-003; PM-ARCH-001

## Direct Owner request

Owner in Prompt Master chat 01a10184-0fb5-7501-9c44-e387cee5a15d requested:
"bạn hãy thực hiện marge các pull request vào nhánh develop"

Preserve accepted roles: Release integrates develop, Reviewer independently approves the exact candidate, Coordinator records readiness. No main, deployment, tag, architecture/API acceptance or permanent authority change is inferred.

## Verified preflight / concrete scope

Public GitHub API on 2026-10-03 returned three PRs total, all closed and merged into developer; no open PRs:
- #1 https://github.com/HieuTran2901/Chrono/pull/1 — SDK component 12893a765a56526ea5630a83f8c24e54a7fc2122.
- #2 https://github.com/HieuTran2901/Chrono/pull/2 — Release plan 18192bf2073fdef57a738b1013cc99de7bc2e6c6.
- #3 https://github.com/HieuTran2901/Chrono/pull/3 — Reviewer component 6b693a820a8693b6d5dd414e6ede2a535c2e58ab.

git ls-remote independently observed developer at 2673bbfc97018815c975eefa2c8b313090a5e8fb; develop/main absent.
The integration approach is to preserve that already-merged history and establish develop at the exact validated/reviewed candidate, rather than remerge or retarget closed PRs.
Do not include other author branches, this handoff, or Architect proposals implicitly. Architecture D1–D8 remain PROPOSED_NOT_ACCEPTED.

## Role handoffs / evidence gates

Release subtask release_develop_prs uses isolated worktree E:/Github project/Chronos-worktrees/release-develop-prs-001 and branch agent/release/develop-prs-001.
Frozen candidate: 2673bbfc97018815c975eefa2c8b313090a5e8fb; target base ABSENT; preserved seed 9a45b111847685255cf1006a2b839d31ecdf31a4.
Release reported seven documentation-only delta paths from the seed and existing PR merge ancestry; these assertions require its final combined validation record.
Independent Reviewer subtask review_develop_prs checks this exact candidate, not previous component approvals.
Coordinator subtask coordinate_develop_prs records assignment and READY_TO_MERGE only after exact Reviewer approval and required validation.
Role-authored review/readiness/release files are published on their respective branches; consumers read immutable SHA:path. No app chat messages are sent under assumed authority.

Release must recheck absent develop immediately before non-forced push, validate result before push, and verify remote SHA after push.
If develop appears or candidate contents change, return to updated review/readiness. Do not modify developer or main.
All executable application/build/compatibility/chaos/load tests remain NOT_RUN while the candidate has no application/build.
Prompt Master records traceability only; no integration or independent review approval by Prompt Master.
