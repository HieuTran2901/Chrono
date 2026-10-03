# Task board

author: coordinator
updated_at: 2026-10-03 20:03 +07:00 (Asia/Saigon)
source_branch: agent/coordinator/project-state
baseline_sha: 9a45b111847685255cf1006a2b839d31ecdf31a4
references: GOV-001; GOV-002; COORD-001; COORD-ROAD-001

Assignments and acceptance: [COORD-ROAD-001](../handoffs/COORD-ROAD-001-coordinator-to-agents.md).
Roadmap/notebook are planning artifacts at 2ce8a98f810cd00d268332ad68df45fc8f99a866, read by git show; not integrated or accepted architecture.
PM-DOC-002 ACK: received at d7d7de6d23a8951d6094a510eb90ec99895550fd.

| ID | Milestone / objective | Role | Branch | State | Dependencies / evidence |
|---|---|---|---|---|---|
| PM-BOOT-001 | Approved framework publication | Prompt Master | agent/prompt-master/bootstrap | DONE (GOV-002 bootstrap exception) | fee3c7e initial publication; baseline 9a45b11 on origin/developer; no independent approval claimed |
| COORD-ROAD-001 | Recover, plan and dispatch roadmap | Coordinator | agent/coordinator/project-state | IMPLEMENTING (documentation) | Assignment 4a18310 published; six sends succeeded, active initial commentary; outputs pending |
| M0-GOV-001 | Confirm developer/develop flow and activate Release | Owner / Coordinator; Release unassigned | none | BLOCKED | Owner naming decision and Release chat required; no rename or shared integration permitted |
| M0-ARCH-001 | Propose exact missing path mapping and foundation | Architect | agent/architecture/m1-foundation | DESIGNING (draft) | GOV-001 ownership; mapping/build choices require Owner acceptance |
| M0-REVIEW-001 | Independently review bootstrap and planning references | Reviewer | agent/review/M0-REVIEW-001 | REVIEW (documents) | baseline 9a45b11; planning d7d7de6; document review only, no feature approval |
| M0-BUILD-001 | Implement approved build/test/CI foundation | Release (unassigned) | agent/release/foundation | BLOCKED | M0-GOV-001; approved M0-ARCH-001 choices and exact paths |
| M1-DESIGN-001 | Draft submit → persist → execute → outcome contracts | Architect | agent/architecture/m1-foundation | DESIGNING (draft) | Final M0 decisions needed before acceptance/implementation; design may be prepared now |
| M1-CORE-INPUT-001 | Engine/scheduler contract questions | Core | agent/core/m1-input | DESIGNING (contract input) | Feedback to M1 design; no production writes |
| M1-DATA-INPUT-001 | Persistence/messaging failure and transaction questions | Data | agent/data/m1-input | DESIGNING (contract input) | Feedback to M1 design; no schema/migration/production writes |
| M1-SDK-INPUT-001 | Client/worker boundary and API questions | SDK | agent/sdk/m1-input | DESIGNING (contract input) | Feedback to M1 design; public API not accepted |
| M1-TEST-PLAN-001 | Failure matrix for first job flow | Reliability | agent/test/m1-failure-plan | DESIGNING (test plan) | Draft matrix now; executable tests wait accepted design/build |
| M2-CORE-001 | Engine/scheduler slice + unit tests | Core | TBD | BLOCKED / not dispatched | Accepted M1 design/contracts, M0 build and explicit task paths |
| M2-DATA-001 | Durable storage/messaging slice + tests | Data | TBD | BLOCKED / not dispatched | Accepted M1 design/contracts and database/transport choices |
| M2-SDK-001 | Minimal submit/worker/outcome client + tests | SDK | TBD | BLOCKED / not dispatched | Accepted public contract and exact paths |
| M2-VERIFY-001 | Negative/failure tests, review and integration | Reliability → Reviewer → Release | TBD | BLOCKED / not dispatched | Exact component/base/candidate SHAs; tests; Reviewer approval; Release available |

## Remaining roadmap backlog (planning, not executable assignments)

| Milestone | Planned roles | Dependency / acceptance |
|---|---|---|
| M3 lease/heartbeat/recovery | Core/Data/SDK; Reliability/Reviewer | M2; concurrent claim/crash/stale-owner evidence; CN-03/04 |
| M4 retry/timeout/cancel/scheduling | Core/Data/SDK; Reliability/Reviewer | M3; attempt and completion/timeout/cancel race evidence; CN-05 |
| M5 messaging/idempotency/outbox/DLQ | Data/Core/SDK; Reliability/Reviewer | Accepted transport contracts and M2–M4; crash/duplicate/ACK evidence; CN-06/07/08 |
| M6 SDK/starter | SDK; module owners/Reviewer | M2–M5; reproducible external integration and lifecycle/compatibility tests; CN-09 |
| M7 workflow/DAG | Architect/Owner then module owners | M3–M6; accepted DAG semantics and fan-in/recovery tests; CN-10 |
| M8 operations/security | Module owners; Release assigned config; Reviewer | Accepted paths/threat model; trace/runbook/security evidence; CN-11 |
| M9 chaos/performance | Reliability; code owners/Reviewer | M3–M8; Owner workload/budget and repeatable measurements; CN-12 |
| M10 release/docs/demo | Coordinator/Reviewer/Release/Owner | M9; exact approved candidate, validated integration, Owner main approval |

M5 minimum may move into M2 if accepted design requires it; architecture determines dependency, not table order.
States use feature.md. READY_TO_MERGE needs exact candidate approval/tests; ordinary DONE needs Release validated/pushed develop evidence (pending Owner branch decision).
No implementation SHA/tests exist. Acceptance is recorded from author-published evidence, never from dispatch alone.
