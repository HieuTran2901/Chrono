# Architect status

author: architect
task_id: M0-ARCH-001; M1-DESIGN-001
status: DRAFT_PREPARED_FOR_PUBLICATION; OWNER_DECISIONS_PENDING
updated_at: 2026-10-03 20:10 +07:00 (Asia/Saigon)
source_branch: agent/architecture/m1-foundation
worktree: E:\Github project\Chronos-worktrees\architect-m1
implementation_sha: none
baseline_sha: 9a45b111847685255cf1006a2b839d31ecdf31a4
references: GOV-001; GOV-002; GOV-003; COORD-ROAD-001; ARCH-PROP-001; ARCH-HANDOFF-001

## Recovery / ACK

ACK COORD-ROAD-001 at 4a18310a0af1f982ab326b77c496b4721fd8ef18; read task board/context/status, rules/prompt/workflows, ROADMAP and CODE_NOTES at 2ce8a98f810cd00d268332ad68df45fc8f99a866. Direct human orchestration authority independently verified in Prompt master chat 01a10184-0fb5-7501-9c44-e387cee5a15d, user turn 01a101d0-d3f9-7220-a142-9f785b02d8cd. ACK GOV-003 decision read at efca8bc9c1758848bce9c7721f19adfdca651277: develop normal integration, developer bootstrap only.

Fetched origin, inspected worktree/branch lists; no Architect branch/worktree existed before creation. Created assigned isolated branch/worktree from baseline. Canonical checkout belongs to Prompt Master; did not switch/stage/commit it or Coordinator checkout. Prior pre-Git conclusion is stale. Role-specific status was absent; this is a real role-start/design milestone. Local baseline contains no application or accepted architecture.

## Completed / publication

Prepared docs/architecture/m1-foundation.md, docs/design/m1-first-job.md, author proposal ARCH-PROP-001, navigation and handoff ARCH-HANDOFF-001. Proposed exact missing path owners, build/dependency alternatives, Job/Attempt lifecycle, atomic ports, wire/error/serialization, security/limits, 13 deterministic failure scenarios and explicit Owner decisions. No production/module/build file, collaboration/Git rule or Owner notebook edited.

Commit/push: pending for initial draft at this status revision. Publication SHA and successful remote verification are recorded in a later author receipt or chat output after commit; never embed a file's own SHA inside that same commit. Design draft preparation does not mean integrated DONE or accepted design.

## Validation / limits

Document checks PASS: six-file owned-path allowlist, all local Markdown links, author/branch metadata and git diff --check on the authored documents before commit. Draft/acceptance consistency manually checked; no acceptance claim exists. Application/unit/integration/chaos/performance tests: NOT_RUN; no executable build or implementation exists. DB/HTTP fault-test infrastructure is specified, not provisioned. Official PostgreSQL/Maven/Spring documents checked 2026-10-03, referenced inside drafts; version manifest and installed-runtime compatibility remain UNVERIFIED.

## Blockers / next safe action

Owner acceptance of D1–D8; exact runtime/dependency manifest; Core/Data/SDK/Reliability published input reconciliation; Release activation and actual develop setup. Naming itself is resolved by GOV-003. Fetch and remote-head snapshot showed no Core/Data/SDK/Reliability/Reviewer input branches yet; do not fabricate ACK/reconciliation. Publish own branch and let Coordinator consume immutable files; revise with actual input before accepted ADR/code assignments. No tool message to another chat is sent under assumed reply permission.

## CN learning hotspots — draft references, code TODO

| ID | Draft hotspot / invariant / race | Trade-off / expected evidence |
|---|---|---|
| CN-01 | Foundation boundaries and proposed POM exceptions, no server imports into SDK | Independent releases vs mapping duplication; dependency graph/build evidence TODO |
| CN-02 | First-job state table I2/I3, Job vs Attempt | Recovery attempt history vs extra entity; lifecycle/terminal immutability tests TODO |
| CN-03 | Atomic port table I3/I4/I6, F03/F06 lock/clock race | Conditional SQL/short locks vs retry handling; real DB concurrent tests TODO |
| CN-06 | Scoped key/command equality I1/I5; F01/F02/F07/F13 | Receipt retention/canonicalization vs storage/compatibility; response-drop replay tests TODO |
| CN-09 | Wire/error and worker lifecycle I8; codec failure F09/F10 | Explicit codec and mapping vs convenience; public compatibility/lifecycle tests TODO |

Code file/symbol/implementation SHA: none. Regression command/result: NOT_RUN. Draft paths are design references, not invented code hotspots. CODE_NOTES is not edited.

60-second proposed explanation: A valid submit commits a durable job and scoped replay receipt together. A worker with free capacity receives a committed attempt; the handler runs outside the transaction. Completion checks current ownership and lease; recovery can grant a later attempt and rejects old writes. This protects stored outcome, while user effects may repeat after a crash. We plan DB race and lost-response tests plus an external idempotent sink to prove the boundary. No code or test evidence exists yet.
