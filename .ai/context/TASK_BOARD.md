# Task board

Owner: Coordinator. Bootstrap records derive from direct Owner requests GOV-001/GOV-002, not new implementation assignments.

| ID | Objective | Agent | Branch | State | Dependencies / blocker | Evidence |
|---|---|---|---|---|---|---|
| PM-BOOT-001 | Install and publish approved agent framework | Prompt Master, one-time Owner authorization | agent/prompt-master/bootstrap → origin/developer | DONE — bootstrap publication exception GOV-002 | Local delivery and requested remote publication complete | Verified initial remote commit fee3c7e0e6b661308d1e09005e8f188889068e8e; no independent review or develop integration claimed |

[Release handoff](../handoffs/PM-BOOT-001-prompt-master-to-release-git-bootstrap.md) records completed bootstrap publication and the remaining standard-workflow setup. No Release Agent is asserted to be active.
Normal branch workflow activation is still pending; it does not make the completed Owner-requested developer push incomplete.

For assigned implementation tasks record: ID, objective, role, explicit paths, branch/worktree, priority, acceptance, accepted design/decision references, dependencies/blocked-by, tests, reviewer, handoff, candidate/base/component SHAs and review/test/release evidence.
States: TODO, DESIGNING, READY, IMPLEMENTING, TESTING, REVIEW, FIX_REQUIRED, READY_TO_MERGE, BLOCKED, DONE.
Coordinator is the ongoing writer. Use evidence from the responsible role for transitions.
READY: sufficient accepted design and permission to implement.
READY_TO_MERGE: exact candidate reviewed/approved and mandatory tests/docs complete.
For ordinary features, DONE remains validated/pushed develop integration with Release evidence. Main release is a separate event.
