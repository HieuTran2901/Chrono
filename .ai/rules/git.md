# Git authority

Accepted baseline: GOV-001. Branch names retain Owner's `agent/*` model.

| Role | Own branches | Shared branch authority |
|---|---|---|
| Prompt Master | agent/prompt-master/<topic> | none |
| Coordinator | agent/coordinator/project-state | none |
| Architect | agent/architecture/<topic> | none |
| Core | agent/core/<feature> | none |
| Data | agent/data/<feature> | none |
| SDK | agent/sdk/<feature> | none |
| Reliability | agent/test/<feature> | none |
| Reviewer | agent/review/<task-id> | none |
| Release | agent/release/<topic> | sole AI role allowed to merge/push develop after gates |

Implementation Agents commit/push only their own branch. No two writers on one branch.
Coordinator documentation also goes through a branch and Release integration; no direct develop exception.
Reviewer commits reports only and never changes production. Architecture docs do not grant production ownership.

Release may assemble a temporary candidate on its own isolated branch/worktree for tests and review before component approval.
This is the Owner-approved staging exception to the original blanket prohibition on merging unreviewed code.
Such staging must not publish to develop/main, deploy, change business semantics or bypass final review.
Actual integration to develop requires READY_TO_MERGE + Reviewer APPROVED for the exact candidate and passing required tests.
If target base or code/test/dependency revisions change, update candidate and obtain relevant validation/re-review before integration.
Run required validation on the merged result before pushing develop.
Semantic conflicts go to the file owner; Release may resolve only obvious formatting/import/documentation metadata conflicts.

Main requires explicit Owner approval for a named release and immutable revision. Default: Owner merges main.
Release may merge main only when explicitly delegated for that approved release. Changed candidate requires renewed approval.
Merge authority does not grant deployment, artifact publication or tag/release publishing authority.

Never force-push shared branches, rewrite shared history, amend another Agent's commits, commit secrets/.env/IDE garbage, commit unrelated paths, or delete/skip failing tests to pass gates.
Before commit: inspect relevant and staged diffs, stage explicit owned paths, run applicable meaningful tests.
Focused commits use feat/fix/test/refactor/docs/perf/chore(scope): description.
A Reliability reproduction test may fail intentionally on its own branch if the target SHA, expected failure and reproduction are documented. The final integrated candidate must pass that test.
Do not commit on main/develop just because the repository starts on that branch.

Repository initialization, remote setup and main/develop bootstrap belong to Release under the approved bootstrap scope. Do not invent a remote URL or hosting permissions.
No existing Git repository was present at the destination when this framework was prepared.
