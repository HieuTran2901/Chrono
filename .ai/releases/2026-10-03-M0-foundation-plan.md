# M0 foundation — develop setup plan and draft document manifest

author: release
task_id: M0-RELEASE-PLAN-001
status: PLAN_COMPLETE; CANDIDATE_NOT_STAGED; INTEGRATION_NOT_AUTHORIZED_BY_THIS_TASK
updated_at: 2026-10-03 20:16 +07:00 (Asia/Saigon)
source_branch: agent/release/foundation-plan
worktree: E:\Github project\Chronos-worktrees\release-foundation-plan
references: GOV-001; GOV-002; GOV-003; COORD-REL-001; M0-REVIEW-001; M0-ARCH-001

## ACK and scope

ACK COORD-REL-001 at e1c65b183ae08c9f9d1ebe836bd5918026c48b20:.ai/handoffs/COORD-REL-001-coordinator-to-release.md, including its TASK_BOARD and CURRENT_STATE.
ACK GOV-003 at efca8bc9c1758848bce9c7721f19adfdca651277:.ai/context/DECISIONS.md: develop remains the normal integration target; developer is bootstrap only.
Read canonical AGENTS, shared rules, recovery/feature workflows, Release prompt, bootstrap handoff and Coordinator immutable sources.
This assignment permits a plan/report and publication on the Release author branch only. It does not assign candidate staging, shared-branch setup/push, build/CI edits, main, deployment, tags or artifact publication.

## Verified Git facts

Checks performed on 2026-10-03 around 20:14–20:16 +07:00, Windows/PowerShell. Origin is https://github.com/HieuTran2901/Chrono.git.
Canonical checkout belongs to Prompt Master on agent/prompt-master/roadmap-notes at 2de958e42f8d032ae9de0ef1b8901731f695d3bc. It was clean on recovery. It was not switched, staged or committed by Release.
Verified worktree list contained no Release branch/worktree writer before creating this isolated worktree from 9a45b111847685255cf1006a2b839d31ecdf31a4.
Release began on agent/release/foundation-plan at that exact baseline, clean.

Network initially failed in the default sandbox (proxy connection to 127.0.0.1:9). A subsequent approved escalated Git check and fetch succeeded. Successful ls-remote returned:

- refs/heads/developer = 9a45b111847685255cf1006a2b839d31ecdf31a4.
- refs/heads/agent/coordinator/project-state = e1c65b183ae08c9f9d1ebe836bd5918026c48b20 in the first successful check.
- No refs/heads/develop or refs/heads/main returned by successful explicit queries.
- No refs/heads/agent/review/M0-REVIEW-001 returned at these checks; local Reviewer report exists but remote publication is not verified.

Remote absence is a point-in-time observation, not permanent authority to create a branch. Recheck immediately before future setup and before push.

## Concrete develop setup plan — future assigned execution

Proposed immutable seed/base: 9a45b111847685255cf1006a2b839d31ecdf31a4, the preserved developer bootstrap history. No rename, deletion or force-push of developer.
Preferred first develop publication is the fully reviewed document candidate descended from this seed, including current governance/context. Avoid publishing a stale seed and calling the ongoing workflow established.

1. Coordinator assigns staging/integration, confirms component manifest and exact paths. Verify no competing Release writer, fetch origin, inspect all source commits by immutable SHA, and query develop/developer/main again.
2. If develop remains absent, record target base as ABSENT and seed as the SHA above. If develop exists, record its full SHA and rebuild/review against that actual base; do not overwrite it. Unexpected ancestry or target movement returns to Coordinator/Reviewer.
3. In a separate isolated candidate worktree, use a new Release branch from the recorded base. Under the later staging assignment, merge exactly the selected planning and Coordinator commits, then selected published review/evidence components if assigned. Example commands below are proposed, NOT_RUN in this task.
4. Record resulting candidate SHA/tree, base/seed, every component SHA, changed-path manifest and conflicts. Do not add arbitrary newer author HEADs. Return semantic conflicts to the author; only obvious formatting/import/documentation metadata conflicts may be resolved by Release, with evidence and re-review.
5. Validate the full combined tree: ancestry, exact changed paths/ownership, whitespace, Markdown links at the candidate tree, decision/source references, absence of accidental code/config/secrets and clear historical versus operational state. Build/runtime tests remain NOT_RUN while no application/build exists; Reviewer must agree with the applicable document validation scope.
6. Obtain Reviewer APPROVED for this exact candidate/base/components and Coordinator READY_TO_MERGE. Separate component approvals cannot replace this gate. New base/content/dependency components invalidate the affected validation/review.
7. Prepare the exact integrated result in an isolated Release checkout, validate it before push. For an absent target, result is the approved initial candidate; for an existing target, record and validate the merge result. Query target again. A changed target blocks this push and requires updated candidate/review.
8. Only after gates, push the recorded result SHA to refs/heads/develop using an ordinary non-forced push. For initial creation, check that the ref is still absent; if it appeared, stop and reconcile even if a fast-forward would be possible. A remote race between check and push remains a concurrency limitation; keep one integration writer and verify the result.
9. Verify ls-remote develop equals the result SHA, publish a Release integration receipt, and let Coordinator record completion. Failure after local merge/validation means no push and return to fix/blocked. Failure/uncertain response during push means read remote before retrying.

Proposed later commands, with placeholders requiring recorded immutable values:

```powershell
git fetch origin
git ls-remote origin refs/heads/develop refs/heads/developer refs/heads/main
git worktree add -b agent/release/m0-document-candidate <isolated-candidate-path> <verified-base-or-seed-sha>
# In the new candidate worktree only:
git merge --no-ff <exact-planning-sha>
git merge --no-ff <exact-coordinator-sha>
git rev-parse HEAD
git diff --check <verified-base-or-seed-sha> <candidate-sha>
# Full-tree document validation and exact Reviewer approval/readiness follow.
# In later authorized integration phase, after result validation/target recheck:
git push origin <validated-result-sha>:refs/heads/develop
git ls-remote origin refs/heads/develop
```

These are Git operations, not invented application build commands. No command in this proposed sequence was executed to stage or publish develop in this task.
Main remains separate: explicit Owner approval for a named release/full SHA, default Owner merge; Release needs specific delegation to merge. No deployment/artifact authority follows from Git integration.

## Draft manifest — M0-DOC-CANDIDATE-001 (not staged)

candidate_sha: NONE
candidate_tree: NONE
target: refs/heads/develop
target_base_sha: ABSENT_AT_SUCCESSFUL_REMOTE_CHECK
seed_sha: 9a45b111847685255cf1006a2b839d31ecdf31a4

| Component | Exact known revision | Purpose and review status |
|---|---|---|
| Bootstrap seed | 9a45b111847685255cf1006a2b839d31ecdf31a4 | Approved governance scaffold; Reviewer local report separately says document APPROVED; candidate approval absent |
| Planning | d7d7de6d23a8951d6094a510eb90ec99895550fd | ROADMAP, CODE_NOTES, Prompt Master status and handoff; local Reviewer document APPROVED only |
| Planning artifact identity | 2ce8a98f810cd00d268332ad68df45fc8f99a866 | ROADMAP/CODE_NOTES original artifacts; not an additional merge component |
| Coordinator operational component | e1c65b183ae08c9f9d1ebe836bd5918026c48b20 | Four operational context updates, two handoffs and Coordinator status; not covered by component review |
| Owner naming evidence | efca8bc9c1758848bce9c7721f19adfdca651277 | GOV-003 ancestor/source already included by Coordinator component; not a separate merge |
| Reviewer evidence, provisional only | 6b693a820a8693b6d5dd414e6ede2a535c2e58ab | Local immutable report read; no remote ref observed, no exact integration approval; requires published revision and explicit manifest selection if to be included |
| Release plan/status | Publication revision to be recorded by consumer or subsequent status | This report/status; not implicitly part of the integration candidate; Coordinator decides evidence components |
| Architect proposals/design | NONE selected | M0-ARCH-001 output/Owner acceptance pending; exclude from this initial document manifest until assigned |

Known delta/ownership analysis from seed:

- Planning: adds ROADMAP.md, CODE_NOTES.md and PM-DOC-002 handoff; modifies Prompt Master status. Treat notebook/roadmap authorship as Owner-requested publication, not permanent general Agent write ownership.
- Coordinator: modifies CURRENT_STATE, DECISIONS, RISKS, TASK_BOARD; adds COORD-REL-001, COORD-ROAD-001 and Coordinator status. These are Coordinator-owned records.
- Planning and Coordinator changed-path sets are disjoint; their merge-base is exactly the seed. This predicts no overlapping textual edits but is not a performed merge/conflict test.
- Current canonical Prompt Master HEAD 2de958e is deliberately excluded from the proposed planning component; substituting it requires manifest revision/re-review.
- Baseline statements in AGENTS/git/context/older handoffs are historical bootstrap snapshots. Coordinator operational state is authoritative from its published branch/SHA. Reviewer must check the combined document navigation and prevent a stale summary being presented as live state.
- Release may combine approved author changes; it does not acquire write ownership of Coordinator, Prompt Master or Owner notebook paths.

## Evidence gaps and dependencies

M0-REVIEW-001 local report approves bootstrap/planning separately with no actionable findings in that scope. Its publication was not verified at this check. It explicitly sets integration_candidate_sha NONE and excludes Coordinator changes from approved components.
Need published review provenance, actual staged candidate/full manifest, combined-tree validation and exact Reviewer approval. No READY_TO_MERGE is claimed.
M0-ARCH-001 still must propose missing module/path ownership and foundation choices for Owner acceptance. This blocks build implementation but does not imply a Java/build/tool decision is needed merely to review documentation.
Coordinator must assign later staging/integration and decide whether review/Release evidence belongs in the initial document candidate or remains on author branches.

## Build/CI/operations mapping request — pending Architect + Owner

Request exact file-level mapping for the accepted build definition(s), module aggregation, wrapper/launcher/version files, CI workflow/configuration, shared test-environment configuration, operations manifests and release packaging/versioning configuration.
Architect should specify the proposed build tool/Java/toolchain/dependencies, module graph, test suites/infrastructure, reproducible developer/CI entrypoints and artifact boundaries. Owner accepts choices and exact ownership; Coordinator then assigns M0-BUILD-001 paths/acceptance.
No pom.xml/build.gradle/settings file, wrapper, .github/workflows path or operations folder is selected or claimed by this request. Release can own explicitly assigned infrastructure configuration only. Production/public SDK contracts remain with their owners/Architect + Owner; Reliability owns cross-module executable test paths.
No root build/CI/operations files are edited; no application version, build command or passing runtime result exists in this report.

## Validation actually performed

- PASS: recovery reads via git show at supplied immutable SHAs; origin/worktrees/branch/status checked.
- PASS: approved isolated worktree creation from the exact baseline, without switching another checkout.
- PASS: escalated git fetch origin and explicit git ls-remote; successful remote facts recorded above. Earlier sandbox failure retained as a limitation resolved for these checks.
- PASS: git diff --name-status seed planning; seed Coordinator; exact document deltas listed above.
- PASS: git merge-base planning Coordinator = 9a45b111847685255cf1006a2b839d31ecdf31a4.
- PASS: git diff --check seed planning and seed Coordinator, no output/errors.
- PASS: git ls-tree baseline and local Reviewer report inspection; no application/build tree at baseline.
- Candidate merges, combined-tree link validation, exact candidate review, post-merge integration validation: NOT_RUN (plan only).
- Application/build/unit/integration/chaos/load tests: NOT_RUN; no accepted application/build/infrastructure at these components.
- Publication and own report/status whitespace validation: recorded in Release status after actual execution.

## Next safe action / CN-01

Publish this author-owned plan/status; Coordinator can accept the preparation deliverable and assign concrete candidate staging/review. Integrate only after the later exact gates. Keep M0-BUILD blocked until exact accepted mapping/design.
CN-01 explanation: a component's document approval says what was checked in that component; integration joins components and can expose inconsistent state/references or a changed base. The full candidate and merged result therefore need their own evidence. Build/module implementation examples remain TODO.
