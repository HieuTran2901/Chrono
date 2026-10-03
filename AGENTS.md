# Chronos Agent entrypoint

Canonical project folder: `E:\Github project\Chronos`.
Owner approved the agent framework (P1/P2/P3) on 2026-10-03. See [.ai/context/DECISIONS.md](.ai/context/DECISIONS.md).
This is an agent framework bootstrap. There is no application implementation or accepted architecture ADR yet.

Before writing:
1. Read [.ai/rules/collaboration.md](.ai/rules/collaboration.md), [.ai/rules/git.md](.ai/rules/git.md) and [.ai/rules/coding.md](.ai/rules/coding.md).
2. Read [.ai/context/PROJECT_CONTEXT.md](.ai/context/PROJECT_CONTEXT.md), [.ai/context/CURRENT_STATE.md](.ai/context/CURRENT_STATE.md), [.ai/context/TASK_BOARD.md](.ai/context/TASK_BOARD.md) and [.ai/context/DECISIONS.md](.ai/context/DECISIONS.md).
3. Read your role prompt in [.ai/prompts/](.ai/prompts/), your status if present, and task-specific handoffs/design/ADR/review.
4. Follow [.ai/workflows/recovery.md](.ai/workflows/recovery.md), verify the actual folder/branch/revisions and report your next safe action.

Only edit assigned paths. An unassigned path is not free for any Agent to claim.
Do not start implementation without a Coordinator assignment, accepted applicable design and known file ownership.
Architect + Owner control architecture/public SDK API decisions. Reviewer never edits production. Only Release integrates/pushes develop. Main requires explicit Owner approval for that release.
No Agent may treat the other tabs' memory, an unaccepted proposal or a stale branch copy as Source of Truth.
Use [.ai/workflows/feature.md](.ai/workflows/feature.md) for evidence gates.
