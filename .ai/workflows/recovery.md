# Startup, context loss and replacement tab

Recover before writes at startup and before each phase if branch/design/evidence changes.
Never guess which checkout, branch, revision or role is active.

1. Verify actual canonical folder or assigned code worktree; inspect branch, status, log and diff when Git exists.
2. Read AGENTS, accepted rules and your role prompt.
3. Read Coordinator-published PROJECT_CONTEXT/CURRENT_STATE/TASK_BOARD/DECISIONS; fetch/read the correct source branch and SHA after Git bootstrap. Read RISKS and ARCHITECTURE as relevant.
4. Read your status, the exact task's handoffs/proposals/review and accepted design/ADR; inspect implementation at the referenced revisions.
5. Report role/ownership, task, actual branch/worktree, completed and unfinished work, implementation commits/push status, tested revisions/results, accepted assumptions/references, blockers and next safe action.
6. If evidence is consistent and work is already authorized, continue without asking Owner again. Missing assignment/source or conflicting authority/design blocks dependent writes; handoff to Coordinator/Architect.
7. For replacement tabs, stop the old writer first; Coordinator records assignment transfer. Never have two active writers on one branch or owned file.

Context loss/staleness signals: task/branch/path mismatch; unknown file owner; repeated rejected proposal; design approval without reference; duplicate abstraction; changed base/candidate; old handoff; unexplained dirty files; writer overlap; claimed pass/approval/completion without evidence.
Read/recovery reporting can continue while dependent coding is stopped.
Dirty work from the current authorized task is not automatically context loss. Preserve unexplained changes; do not stash/discard/commit another writer's work.

Bootstrap exception: when Git/remote do not exist, state that explicitly and read the local approved baseline in the canonical folder. Do not claim commit SHA, remote ACK, review approval or integration. No parallel implementation starts in this state.

New-tab instruction:
“You are replacing <role> for <task>. Recover through .ai/workflows/recovery.md, verify real folder/branch/ownership/evidence and report the next safe action before edits. Previous-tab memory is not Source of Truth.”
