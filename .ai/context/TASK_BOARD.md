# Task board

Owner: Coordinator. Bootstrap record derived directly from Owner request, not a new implementation assignment.

| ID | Objective | Agent | Branch | State | Dependencies / blocker | Review/evidence |
|---|---|---|---|---|---|---|
| PM-BOOT-001 | Install approved agent framework at E:\Github project\Chronos | Prompt Master, one-time bootstrap | none; Git absent | BLOCKED for Git publication/integration | GOV-001 accepted; local framework prepared; Release Git bootstrap required | Local document/link/content verification; no independent Reviewer approval |

The local file delivery can be complete while this task remains blocked for publication; do not call it integrated DONE.
[Release bootstrap handoff](../handoffs/PM-BOOT-001-prompt-master-to-release-git-bootstrap.md) requests the next phase; it does not assert Release has been activated.

For assigned tasks record: ID, objective, role, explicit paths, branch/worktree, priority, acceptance, accepted design/decision references, dependencies/blocked-by, required tests, reviewer, handoff, candidate/base/component SHAs, review/test/release evidence.
States: TODO, DESIGNING, READY, IMPLEMENTING, TESTING, REVIEW, FIX_REQUIRED, READY_TO_MERGE, BLOCKED, DONE.
Coordinator is the only writer. Use evidence from the responsible role for transitions.
READY: sufficient accepted design and permission to implement.
READY_TO_MERGE: exact candidate reviewed/approved and mandatory tests/docs complete.
DONE: integrated and validated/pushed to develop, with Release evidence. Main release is a separate event.
