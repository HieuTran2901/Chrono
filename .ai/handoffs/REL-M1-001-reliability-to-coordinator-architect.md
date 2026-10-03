# REL-M1-001 — Reliability failure-plan input

author: reliability
recipient: coordinator; architect
task_id: M1-TEST-PLAN-001
status: PREPARED; PUBLICATION_BLOCKED_AUTO_REVIEW; NOT_RUN
updated_at: 2026-10-03 20:04 +07:00 (Asia/Saigon)
source_branch: agent/test/m1-failure-plan
references: COORD-ROAD-001; M1-DESIGN-001; M2-VERIFY-001

ACK assignment 4a18310a0af1f982ab326b77c496b4721fd8ef18:.ai/handoffs/COORD-ROAD-001-coordinator-to-agents.md.
Immutable baseline: 9a45b111847685255cf1006a2b839d31ecdf31a4. Planning inputs: 2ce8a98f810cd00d268332ad68df45fc8f99a866:ROADMAP.md and CODE_NOTES.md.
Output: ../../test-support/M1-failure-matrix.md and ../status/reliability.md in this authored component revision. Consumer records this file's publication SHA; no self-referential commit SHA embedded.

## Requested work and acceptance

Architect: address Q-01–13 against draft design, then link accepted decisions/oracles before executable tests. For first job, prioritize payload/unknown-handler policy, durable acknowledgment/query visibility, handler failure/duplicate completion and submit dedup/lost response. Identify what crash recovery/claim/messaging is necessary in M2. Define exact boundaries, errors, histories and bounded observation/recovery expectations without assuming exactly-once external effects.
Coordinator: record this preparation output and assign executable test paths only after accepted design/build/ownership gates. M3/M5 scenarios can move into M2 when design requires them. Review this component as documentation; no feature pass/readiness is implied.
Owner decisions to consolidate via Architect/Coordinator: public behavior/API, persistence/transport/ownership model and scope, plus normal integration branch, Release activation, unmapped paths and future workload/budget.

Acceptance coverage: 25 scenarios cover first-job control/negative/errors, duplicate/lost response, commit crash, scheduler competition, stale heartbeat/completion, worker/scheduler pause/crash/restart, retry/timeout/cancel, commit/publish/ACK, outage/poison/replay and later SDK/DAG/security/load. Each links proposed invariant/question, injection/observation/infrastructure and scope. Expected outcomes remain TBD and tests NOT_RUN.

## Risks and evidence limits

No accepted design/ADR, code/test component or infrastructure exists in inspected references. Matrix is not an executable guarantee, schema, API or technology choice. Critical risk: conflating stored ownership/result protection with preventing repeated external effects. Conditional messaging rows do not select outbox or a broker.
CN-02–08/12 hotspots and missing code/test evidence are recorded in Reliability status; notebook untouched. No production fixes, Reviewer approval, normal integration or main/deployment claimed.
Next: read this publication by SHA; resolve M2 oracles; publish accepted design and exact future task assignment. Production bugs discovered later go to their file owners with preserved reproduction.

Local artifact commit: 343edd9d738f6bac1f74b6dd216de5ee5d5a72e2. Remote push was rejected by automatic approval review; publication requires direct Owner confirmation in this chat. Read local artifact by git show if available; do not claim remote delivery.
