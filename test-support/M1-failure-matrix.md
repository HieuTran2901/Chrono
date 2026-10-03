# M1 failure matrix — first job and later milestones

author: reliability
task_id: M1-TEST-PLAN-001
status: DRAFT_PREPARATION; NOT_RUN
updated_at: 2026-10-03 20:04 +07:00 (Asia/Saigon)
source_branch: agent/test/m1-failure-plan
baseline_sha: 9a45b111847685255cf1006a2b839d31ecdf31a4
assignment_sha: 4a18310a0af1f982ab326b77c496b4721fd8ef18
planning_sha: 2ce8a98f810cd00d268332ad68df45fc8f99a866
implementation_sha: none

## Sources and interpretation

Assignment: COORD-ROAD-001 at assignment_sha, .ai/handoffs/COORD-ROAD-001-coordinator-to-agents.md and .ai/context/TASK_BOARD.md. ACK recorded in Reliability status.
ROADMAP.md and CODE_NOTES.md at planning_sha are planning references, not accepted contracts. Governance/rules/prompt/recovery/feature workflow read at baseline_sha; operational context read at assignment_sha by git show. Branch-local bootstrap context is historical; do not use it as current task state.

WHY: make failure windows and missing contract decisions reviewable before code.
WHAT: proposed behavior checks for submit -> persist -> execute -> query outcome, then distributed recovery and later milestones.
FLOW: arrange controlled input/state -> stop at a named boundary -> inject one fault -> resume/restart -> observe durable state, public response and external effect separately.
FAILURE: unknown acknowledgment, duplicate delivery, concurrent ownership, stale completion and partial external effects can diverge.
TRADE-OFF: deterministic barriers give reproducible interleavings; later real-process/network chaos tests broader behavior but needs approved infrastructure and budgets. Neither a mock nor a log proves durability.

All P-* invariants below are PROPOSED for Architect + Owner discussion. All rows are NOT_RUN; every final expected outcome is TBD until accepted design supplies its oracle, error mapping, time bounds and scope. No status/entity/API signature, storage, broker, version, exactly-once guarantee or performance target is selected here. Human descriptions of outcomes are not implementation state names.

M2 rows propose first-demo coverage. M3-M9 rows are later backlog, not executable assignments. If accepted M2 design needs distributed claim, recovery or messaging, Coordinator must move the relevant cases into M2 rather than defer correctness solely by milestone number.

## Proposed invariants and decision questions

| Ref | Proposed invariant / question requiring accepted design | Decision input |
|---|---|---|
| P-01 / Q-01 | Validation has a defined persistence/execution boundary; invalid data cannot trigger an unintended handler. Which payload rules, error mapping and unknown-handler policy (reject vs persist/wait/fail) apply? | Architect + SDK/Core; Owner accepts public behavior |
| P-02 / Q-02 | Acknowledged durability and query visibility have explicit meanings. At what point may success be returned; what survives restart; what is an indeterminate response; what query consistency/recovery bound applies? | Architect + Data/SDK |
| P-03 / Q-03 | Legal outcome transitions and handler errors are observable. Which errors retry, what is recorded, and how does the client distinguish handler failure from transport failure? | Architect + Core/SDK |
| P-04 / Q-04 | Submit dedup scope is separate from handler and external-effect dedup. Are keys supported; how are payload conflicts, concurrent requests, retention and expired keys handled? | Architect + Data/SDK; Owner accepts public contract |
| P-05 / Q-05 | Claim and completion obey accepted ownership rules under races. What atomicity, identity/version checks, lease clock and fencing scope apply; can executions overlap? | Architect + Core/Data |
| P-06 / Q-06 | Recovery preserves accepted durable work and rejects obsolete writes according to contract. Who recovers; when; what bounds and pause/skew assumptions apply? | Architect + Core/Data/SDK |
| P-07 / Q-07 | Retry, timeout and cancel have consistent history and precedence. What wins at boundaries; which effects can continue; what limits and cleanup are promised? | Architect + Core/SDK |
| P-08 / Q-08 | Business state and event intent have a defined commit/recovery relation if messaging is selected. What atomicity, event identity, replay/checkpoint and ordering scopes apply? | Architect + Data/Core |
| P-09 / Q-09 | ACK/redelivery, poison events and external effects follow an explicit consumer contract. Where is dedup enforced; what does replay do; what failures reach DLQ? | Architect + Data/SDK/Core |
| P-10 / Q-10 | Public integration and lifecycle have explicit compatibility and resource cleanup behavior. Which versions, serialization rules, reconnect/drain and error mappings are supported? | Architect + SDK; Owner accepts API |
| P-11 / Q-11 | Workflow dependencies gate child execution according to accepted DAG rules. What fan-in and partial-failure/recovery semantics apply? | Architect + Owner, then module owners |
| P-12 / Q-12 | Evidence is correlated without exposing protected data. Which IDs, telemetry, access control, redaction and cardinality requirements apply? | Architect + SDK/Core/Data; Owner scope decision |
| P-13 / Q-13 | Performance is judged against an approved workload and budget while correctness remains checked. What arrival model, dataset, latency boundary, SLO and run cost apply? | Owner + Architect; Reliability measures |

## Infrastructure profiles (requirements, not selections)

| Profile | Required capability before execution |
|---|---|
| I-A | Approved client/server/worker build and interfaces; controlled valid/invalid payloads, registered/unregistered handlers, response/query capture and isolated fixtures. |
| I-D | Actual selected durable store with production-relevant transaction/isolation settings; safe state observations and commit barriers; independent process restart. No database product/version chosen. |
| I-R | Independent scheduler/worker processes; controllable pause/kill/restart, ownership observations and virtual clock where supported. Real-time expiry checks supplement simulated time. |
| I-N | Scoped request/response/network fault control with delivery markers; confirms which request/commit/publish occurred before dropping a response. |
| I-E | External-effect recorder, independent of Chronos state, with stable correlation and optional downstream idempotency/fencing capability if design requires it. |
| I-M | Actual selected transport when applicable; delivery/ACK checkpoints, event IDs, redelivery/out-of-order controls and restartable publisher/consumer; DLQ only if selected. |
| I-L | Isolated load environment, approved generator/workload/budget, resource and latency collection, warm-up and drain protocol. |

## Scenario matrix

Each row names deterministic injection and observations; proposed expectations are discussion targets, not pass criteria. Questions refer to the table above. Do not replace TBD with a guessed guarantee.

| ID / planned scope / CN | Failure and deterministic timeline | Proposed invariant / expected observable outcome (final TBD) | Observations / required infrastructure | Open design questions |
|---|---|---|---|---|
| F-01 M2 CN-02 | Baseline control: submit valid input, allow one handler invocation, query outcome, restart and query again. | P-02/03: correlate accepted input, execution and durable result; final identity/visibility/durability oracle TBD. | Response/query, invocation recorder, durable snapshot before/after restart; I-A/D/E. | Q-02/03: acknowledgment, result retention and consistency bounds. |
| F-02 M2 CN-02 | Submit malformed, missing-required, wrong-type and over-limit payloads separately; barrier before execution. | P-01: defined rejection/recording boundary and no unintended invocation; exact errors and allowed persistence TBD. | Request/error, durable records and zero/unexpected handler effects; I-A/D/E. | Q-01: schema, size limits and serialization validation layer. |
| F-03 M2 CN-02 | Submit identifier with no registered handler; separately unregister after accepted submit but before dispatch. | P-01/03: observable unknown-handler behavior without silent success; reject/wait/fail outcome TBD. | Registration timeline, response, query and invocation count; I-A/D/E. | Q-01/03: admission vs dispatch validation; registration lifetime. |
| F-04 M2 CN-02 | Handler throws before external effect; separate variant records an effect then throws before completion. | P-03/04: handler error distinguishable from transport error; retry/result/effect guarantees TBD. | Exception class mapping, outcome/history and independent effect count; I-A/D/E. | Q-03/04: retry in M2? side-effect contract and failure visibility. |
| F-05 M2 CN-06 | Replay identical submit serially, then concurrently at a pre-admission barrier; variant uses same proposed key with different payload. | P-04: accepted duplicate/conflict semantics, identity and execution multiplicity TBD; never assume dedup exists. | All responses, stored identities, invocation/effect count; I-A/D/E. | Q-04: key support, scope, payload comparison and retention. |
| F-06 M2 CN-03/06 | Verify commit/accept marker, drop only submit response, then resend input; contrast request dropped before server receipt. | P-02/04: indeterminate client result and recovery lookup/retry behavior explicit; duplication allowance TBD. | Server receive/commit markers, retry response/query, durable IDs/effects; I-A/D/N/E. | Q-02/04: timeout mapping and safe retry/lookup contract. |
| F-07 M2 CN-03 | Stop process immediately before persistence commit; restart and inspect; second run stops after commit before any dispatch. | P-02: precommit rollback and postcommit durability/recovery evaluated against actual transaction boundary; exact outcomes TBD. | Store state, acknowledgment record and postrestart dispatch; I-A/D/R. | Q-02/06: transactional boundary and whether recovery must already exist in M2. |
| F-08 M2 CN-02/03 | Persist handler outcome, drop completion response; worker retries completion; restart query service before final query. | P-02/03/05: response loss cannot invent a new valid transition; duplicate completion/query oracle TBD. | Completion request IDs, result/version/history and effects; I-A/D/N/E. | Q-02/03/05: completion idempotency, conflict response and read consistency. |
| F-09 M3; M2 conditional CN-03/04 | Two schedulers reach same eligible-work barrier, release together; repeat both controlled winner orders. | P-05: ownership follows accepted atomic claim; claim/execution/effect multiplicity bounds TBD. | Claim successes, owner/version, invocation intervals/effects; I-A/D/R/E. | Q-05: linearization point and overlap/fencing scope. |
| F-10 M3 CN-04 | Worker A claims, pauses past accepted expiry; B recovers; release A's old heartbeat then old completion, in both orders. | P-05/06: obsolete writes cannot overwrite current ownership/result under proposed contract; external effects separately TBD. | Ownership generations, heartbeat/result decisions, durable history/effect ledger; I-D/R/E. | Q-05/06: token checks, expiry source and downstream fencing. |
| F-11 M3 CN-03/04 | Kill worker after claim before invocation; repeat after effect before durable completion; restart or replace worker. | P-06: recovery policy and duplicate-effect allowance are explicit; exact recovery time/result TBD. | Claim/outcome timeline, invocation/effect count and backlog after recovery; I-A/D/R/E. | Q-04/06: unknown execution, recovery eligibility and bounded progress. |
| F-12 M3 CN-03/04 | Kill scheduler after claim before dispatch; separate variant after dispatch before local acknowledgment/checkpoint. | P-05/06: durable claim/dispatch recovery evaluated; loss/redelivery/overlap allowance TBD. | Claim, dispatch markers and recovered invocation/history; I-D/R/N/E. | Q-05/06: dispatch ownership and recovery actor. |
| F-13 M3 CN-04 | Pause process or partition renew path while handler runs; recover ownership then resume. Test lease boundary just before/at/after expiry; skew variant only within accepted clock model. | P-05/06: expiry and renew ordering deterministic according to design; state protection distinct from effect safety, oracle TBD. | Clock inputs, renew decisions, ownership/version and overlapping invocation intervals; I-D/R/N/E. | Q-05/06: clock authority, allowed skew and paused-process assumptions. |
| F-14 M4 CN-05 | Retryable error repeatedly; advance approved clock to before/at/after retry deadline; restart while waiting; inject late old retry trigger. | P-03/07: bounded attempts, schedule/history and stale-trigger disposition according to policy, TBD. | Attempt identities, schedule times, retry reasons and resource counts; I-A/D/R/E. | Q-03/07: classification, max attempts, backoff/jitter and time boundary. |
| F-15 M4 CN-05 | Hold completion and timeout at barriers, release timeout first, completion first, then concurrently; keep handler/effect running past timeout. | P-07: consistent winning transition/history; late result and ongoing effect disposition TBD. | Durable outcome/history, cancellation signal, effect/resource ledger; I-A/D/R/E. | Q-07: precedence and whether timeout stops work or only changes recorded outcome. |
| F-16 M4 CN-05 | Race cancel with claim, completion and retry separately; restart after cancel acknowledgment; release delayed completion/retry. | P-07: cancel acknowledgment, late transitions and cleanup obey accepted contract, TBD. | Responses, state/history, invocation/effects, threads/connections after drain; I-A/D/R/N/E. | Q-07: queued vs running cancel, idempotency and cleanup bounds. |
| F-17 M5; M2 conditional CN-07 | If event intent selected: kill before commit of business state + intent, then after commit before publisher reads. | P-08: accepted atomicity/recovery relation and retained intent are observable, TBD; pattern itself not chosen. | State/intent snapshots, publish markers after restart; I-D/R/M. | Q-08: shared transaction or alternative durability protocol. |
| F-18 M5; M2 conditional CN-07/08 | Confirm transport accepted publish, kill publisher before recording checkpoint; restart and deliver duplicate. | P-08/09: replay/duplicate behavior preserves accepted consumer invariant, TBD; no exactly-once inference. | Event identity, publish count, checkpoint and consumer effect ledger; I-D/R/M/E. | Q-08/09: identity, publisher checkpoint and consumer dedup boundary. |
| F-19 M5; M2 conditional CN-06/08 | Crash consumer before processing; after durable processing/effect but before ACK; contrast ACK before failed processing if protocol permits. | P-09: redelivery/loss risk and effect consistency follow documented ACK boundary, TBD. | ACK/delivery markers, durable result and independent effects after restart; I-D/R/M/E. | Q-09: ACK atomicity and external-target coordination. |
| F-20 M5 CN-06/08 | Deliver same event repeatedly, reorder within/outside accepted ordering scope; inject poison payload, then controlled replay. | P-08/09: bounded failure handling and ordering/dedup/DLQ disposition TBD. | Event IDs, attempts, ordering evidence, queue/DLQ and replay effects; I-D/M/E. | Q-08/09: ordering scope, retention, poison policy and replay authorization. |
| F-21 M5; M2 conditional CN-03/07/08 | Cut store or selected transport connectivity at commit/publish/ACK boundaries separately; restore after controlled outage. | P-02/08/09: ambiguous acceptance and recovery/backpressure explicit; progress/loss/duplicate bounds TBD. | Receive/commit/publish/ACK markers, errors, backlog and postrestore effects; I-D/N/R/M/E as applicable. | Q-02/08/09: retry budgets, ambiguous commit and outage recovery bounds. |
| F-22 M6 CN-06/09 | Misconfigure worker/serialization; drop connection during startup/execution, reconnect then shutdown at invocation barrier; test approved compatibility combinations. | P-10: startup errors, reconnect/drain, cleanup and compatibility outcomes TBD. | Public errors, registration timeline, invocation/resource ledger; I-A/D/R/N/E. | Q-10: supported versions, payload evolution and graceful/forced shutdown. |
| F-23 M7 CN-02/03 | Concurrent parent completions at fan-in barrier; duplicate one completion; restart between dependency satisfaction and child dispatch. | P-11: child eligibility/multiplicity and partial failure recovery TBD. | Dependency/progress state, child dispatch/effect ledger; I-A/D/R/E; I-M only if selected. | Q-11: accepted DAG/fan-in and failed-parent behavior. |
| F-24 M8; minimal M2 CN-02/12 | Inject failures with sentinel sensitive fields; trace submit through outcome; invalid credentials/authorization attempts only for approved exposed interfaces. | P-12: required correlation/redaction/access checks observable; exact policy and errors TBD. | Response/query/log/metric/trace samples and execution/effect absence for denied input; I-A/D/E. | Q-12: threat model, sensitive fields, access and metric cardinality. |
| F-25 M9 CN-12 | Run approved workload baseline then one controlled process/network fault per run; measure warm-up, steady state, outage and drain separately. | P-13 plus relevant invariants: budget and recovery/regression criteria TBD; no invented throughput/SLO. | Raw latency/error/resource/backlog series, effect ledger and fault timeline; I-L plus actual selected profiles. | Q-13: workload, environment, cost, latency definition and budget. |

## Execution readiness and evidence contract

Before any executable case: Coordinator assigns exact test/support paths and accepted design references; approved build/infrastructure are available; each TBD is resolved or case explicitly deferred by authorized scope decision. Name base/candidate/code/test/dependency SHAs and approved environment. A row may become N/A only with documented accepted scope rationale, never merely because infrastructure is unavailable.

Future run record must contain: scenario ID; accepted invariant/ADR section and expected oracle; exact revisions; actual test path/symbol and command; store/transport/runtime versions and isolation/config; seeded data; fault barrier and schedule; clock model; start/end timestamps; expected vs actual responses/state/invocations/effects; cleanup/drain verification; raw artifacts and result PASS/FAIL/UNVERIFIED. Commands, tests and artifact paths are currently TBD/NOT_RUN, not fake executable examples.

Avoid sleeps as the sole race control: wait for confirmed boundary markers, then release barriers in each relevant order. Apply bounded waits derived from the accepted contract, capture timeout evidence, and distinguish oracle timeout from product failure. Lost-response cases must prove the server reached the intended boundary. Abrupt process-kill durability cases must use the real selected infrastructure; mocks can aid fault-hook checks but cannot establish crash/transaction guarantees. Observe external effects independently of completion state.

Preserve a failing reproduction with target SHA and expected failure; hand off bugs to the production file owner. Retest fixed revision and affected cases; changed candidate/base/dependencies requires relevant validation and Reviewer recheck. Final integrated candidate must pass mandatory reproductions. Planning does not grant review, integration or deployment approval.

## Owner / Architect decisions and next step

Architect + Owner need to accept public validation/error/result/dedup behavior (Q-01–04), ownership/recovery and effect limits (Q-05–06), retry/timeout/cancel precedence (Q-07), selected persistence/transport and delivery semantics (Q-08–09), lifecycle/compatibility and DAG scope (Q-10–11), minimum security/telemetry (Q-12), and workload/budget (Q-13). Architect coordinates Core/Data/SDK input; Reliability supplies observable cases, not architecture choices.

Owner governance decisions also remain developer/develop normal integration, Release activation and exact missing path mapping. These block future integration/build as applicable, not this assigned planning artifact.

Next safe action: Coordinator/Architect read this published matrix, resolve M2-required oracles and which conditional M3/M5 cases belong in first-job acceptance; Coordinator then assigns executable cases after design/build gates. No production, SDK chronos-test or Owner notebook changes.
