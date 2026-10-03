# Collaboration, ownership and Source of Truth

Policy baseline: GOV-001, accepted by Owner on 2026-10-03. Owner instructions take precedence.
Prompt Master initializes this approved documentation scaffold once; afterward the following ownership applies.
Changes to roles, ownership, Git authority or architecture require an Owner-approved proposal before application.

## Role and path ownership

| Role | Write ownership | Decision limits |
|---|---|---|
| Prompt Master | `.ai/prompts/`, this file, `.ai/rules/git.md`, `.ai/workflows/` | Improve cooperation; do not assign implementation tasks, maintain project state, design features, approve review or integrate |
| Coordinator | `.ai/context/` except ARCHITECTURE.md; README.md | Assign tasks, track dependencies, record Owner decisions; do not decide architecture/API or edit production |
| Architect | `docs/architecture/`, `docs/design/`, `docs/adr/`, `.ai/context/ARCHITECTURE.md`, `.ai/rules/coding.md` | Design and evaluate contracts; architecture/API changes require Owner acceptance; no production implementation |
| Core | `chronos-server/chronos-engine/`, `chronos-server/chronos-scheduler/` | Assigned code and colocated unit tests; no storage/SDK changes |
| Data | `chronos-server/chronos-storage/`, `chronos-server/chronos-messaging/`, `database/migrations/` | Storage/messaging implementation and tests; no unilateral domain/architecture change |
| SDK | `chronos-sdk/chronos-java-sdk/`, `chronos-sdk/chronos-worker-sdk/`, `chronos-sdk/chronos-spring-boot-starter/`, `chronos-sdk/chronos-test/` | SDK implementation/tests; no unilateral public contract change |
| Reliability | `integration-tests/`, `load-tests/`, `chaos-tests/`, `test-support/` | Reproduce/verify failures; never fix production or edit SDK chronos-test |
| Reviewer | `.ai/reviews/`; report paths only if explicitly assigned | Report and verify; never edit production, fix tests for coders or integrate |
| Release | `.ai/releases/`; build/CI/release configuration only at explicitly assigned paths | Stage candidates and integrate approved work; no feature development or semantic conflict fixes |

Every role additionally owns its `.ai/status/<role>.md`, and proposals/handoffs it authors. Receivers do not edit senders' files.
Only one active writer per branch, task assignment and owned file. Replacing a tab requires the old writer to stop.
These implementation paths come from the Owner's original workflow; the modules do not yet exist.
Unmapped server API, shared contracts, root build/CI, operations and documentation paths remain unassigned until Owner approves exact mapping with Architect input where module boundaries are involved. Do not invent a module to complete the map.
Coordinator can schedule work within accepted ownership; permanent reassignment needs Owner approval.

## Source of Truth

| Fact | Authoritative source | Writer / authority |
|---|---|---|
| Role/path/Git permissions | accepted rules here and git.md | Prompt Master maintains; Owner approves authority changes |
| Architecture | accepted docs/architecture and docs/design; ADR records decisions | Architect writes; Owner accepts major direction/API |
| Owner governance/scope/API decisions | context/DECISIONS.md linking proposal and Owner approval evidence | Coordinator records; Owner decides |
| Task, assignment, dependencies, workflow state | context/TASK_BOARD.md | Coordinator |
| Agent progress | author's status file at published branch/SHA | Author; does not itself approve or integrate |
| Review | reviews/<task>-<candidate>.md tied to immutable revisions | Reviewer |
| Release/integration | releases/<date>-<task-or-release>.md plus Git/test evidence | Release; Owner approves main |
| Overview | PROJECT_CONTEXT/CURRENT_STATE, with references | Coordinator; summary never overrides original evidence |

Architecture.md is a navigation map, not a second architecture specification. DECISIONS links ADRs rather than repeating their contents.
Proposals are not active rules before acceptance. Rejected/superseded decisions stay discoverable.
If accepted sources conflict, stop dependent writes and record both sources; ask the decision owner to resolve, rather than choosing by timestamp.
Git/code proves what exists; it does not prove design approval.

## Publish/read protocol

Use separate code checkouts/worktrees. Do not switch a shared checkout under another Agent.
After Git bootstrap:
1. Read accepted rules from the integrated develop revision.
2. Read operational project state from `agent/coordinator/project-state`.
3. Author writes only their owned status/proposal/handoff/review/release file and commits/pushes their own branch.
4. Consumer fetches and reads the published file by immutable SHA: `git show <source-sha>:<repo-relative-path>`. Do not merge merely to read a message.
5. ACK goes in receiver's status or a receiver-authored reply, referencing message ID and source SHA.
6. Coordinator links author branch/SHA in task state; do not copy findings into several competing sources.
7. Changed contracts/evidence require a new published revision and a receiver recheck. A local commit is not a successful push.

Before Git/remote bootstrap, local files are the bootstrap baseline only. Do not claim remote publication, integrated policy or cross-machine delivery. Keep one writer and do not launch parallel implementation.

## Minimal file records

Use stable IDs and metadata: author, task_id, status, updated_at with timezone, source_branch, references.
Status: worktree, implementation_sha, completed, files/commits/push status, tests (command/result/target/environment), blockers/risks/dependencies, next action.
Handoff: recipient, context, requested work, immutable source revisions, contracts, acceptance criteria, related design/ADR, risks.
Proposal: problem/current rule/proposed rule, alternatives, affected roles/paths/authority, benefits/risks, compatibility/migration, decision required.
Review: candidate/base/component SHAs, test evidence, findings with evidence/impact/severity/disposition, outcome.
Release: source/target/result SHAs, review reference, validation, conflicts, push result, risks, main approval if applicable.
Consumers record the file's publication SHA after commit; never try embedding a file's own commit SHA into that same commit.

Create event files only for real events. Update status at handoff, blocker, review, fix/retest and integration milestones; not after every message.
Owner reports: issue → cause → affected Agents → proposal → benefit/risk → autonomous action or approval needed.
