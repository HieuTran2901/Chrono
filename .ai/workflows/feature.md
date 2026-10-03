# Design → Implement → Test → Review → Fix → Approve → Integrate

Use accepted roles/paths from collaboration.md and authority from git.md.
For small fixes, reuse applicable accepted design/ADR; no new ADR is required just to satisfy a template.

| Phase | Responsible | Evidence gate |
|---|---|---|
| Design | Architect; Coordinator defines task; Owner decides architecture/API changes | WHY/WHAT/FLOW/FAILURE/TRADE-OFF, invariants/contracts, acceptance, ownership, accepted decisions |
| Implement | Assigned Core/Data/SDK owner | Own code and unit tests on own branch; handoff for foreign paths/contract changes |
| Test | Coder + Reliability within respective ownership | Required behavior/failure tests on recorded SHAs/environment; unavailable is UNVERIFIED |
| Review | Reviewer | Candidate ID/base/component SHAs, test evidence, findings/disposition |
| Fix | Owner of faulty files | Preserve reproduction, fix own branch, retest and return candidate to Review |
| Approve | Reviewer; Coordinator records readiness | APPROVED for exact candidate; mandatory tests/docs met; no unresolved CRITICAL/HIGH |
| Integrate | Release | Match approved revisions/base, merge, validate result before push develop, publish result/release report |

Review outcomes: APPROVED or CHANGES_REQUIRED. LOW/SUGGESTION may remain as documented notes.
MEDIUM must be fixed or explicitly deferred with Owner's recorded acceptance of that concrete risk; Reviewer verifies the disposition. Unresolved CRITICAL/HIGH always blocks.
Coordinator cannot replace Reviewer approval with a task state. Release cannot approve its own candidate.

## Multiple component branches

Task/review record contains a compact manifest: candidate ID, develop base SHA, implementation/test component SHAs and accepted design/decision references.
Release assembles an isolated candidate in its own branch/worktree for Reliability tests and Reviewer inspection. This staging permission does not allow unreviewed develop/main integration or production fixes by Release.
Tests owned by Reliability stay on its own branch; final candidate includes their exact revision.
Reviewer approves the complete tested candidate; Release merges only approved revisions, not an arbitrary newer branch HEAD.
Changed code/test/dependency or changed target base requires updated candidate, relevant tests and Reviewer recheck. Operational status metadata must not cause unreviewed code to enter the merge.
Semantic conflicts: stop, record evidence, return to path owner, rerun tests/review after resolution.
A failed post-merge local validation must not be pushed to develop; report candidate/result and return task to Fix/Blocked without rewriting shared history.

## Release to main

After develop integration validation, Release prepares a release record with immutable SHA/version/scope/tests/risks.
Owner approves that named release. Default Owner performs main merge; Release does so only if explicitly delegated.
Changed release SHA requires renewed approval. Deployment/artifact publication requires its own authorization.
