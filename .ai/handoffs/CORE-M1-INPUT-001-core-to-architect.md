# CORE-M1-INPUT-001 — Engine/scheduler contract input

author: core
task_id: M1-CORE-INPUT-001
recipient: architect; coordinator; Data/SDK/Reliability consume as design input
status: PROPOSED_INPUT; PREPARATION_COMPLETE; NOT_ACCEPTED_DESIGN
updated_at: 2026-10-03 20:03 +07:00 (Asia/Saigon)
source_branch: agent/core/m1-input
references: GOV-001; GOV-002; COORD-ROAD-001; M0-ARCH-001; M1-DESIGN-001

## ACK and immutable sources

ACK COORD-ROAD-001 and task assignment at 4a18310a0af1f982ab326b77c496b4721fd8ef18:.ai/handoffs/COORD-ROAD-001-coordinator-to-agents.md. Read the task board, operational context, decisions and risks at that same SHA using git show; no source branch merged.

- Rules, Core prompt and recovery/feature workflows: baseline 9a45b111847685255cf1006a2b839d31ecdf31a4, at AGENTS.md, .ai/rules/{collaboration,git,coding}.md, .ai/prompts/core.md and .ai/workflows/{recovery,feature}.md.
- Planning: 2ce8a98f810cd00d268332ad68df45fc8f99a866:ROADMAP.md (M1–M5); CODE_NOTES.md (CN-02/03/04/05).
- No accepted M1 design/ADR or application implementation was present in the read sources. Logical operations and expected outcomes below are proposals/questions, not chosen schema, public signatures, technologies or guarantees.
- Owner orchestration instruction independently read in Prompt master chat 01a10184-0fb5-7501-9c44-e387cee5a15d. Returned user item ID 01a101d0-d625-7210-9bd4-a7424cfedac2 contains the quoted dispatch authorization. Coordinator cites turn 01a101d0-d3f9-7220-a142-9f785b02d8cd; these IDs differ. Content verified; record the returned ID without editing sender evidence or assuming both IDs identify the same object.

## WHY / WHAT / FLOW / FAILURE / TRADE-OFF

WHY: a successful demo must explain when work becomes durable, who can authorize execution and which result can change durable state. Otherwise a scheduler competition or lost response can become an undocumented duplicate or lost job.
WHAT: request a minimal accepted vocabulary, transition table and logical Core/Data/SDK contracts for submit → persist → execute → outcome. Do not instantiate every roadmap concept as an entity.
FLOW (proposed outline): validate submit → durably accept → choose eligible work → atomically authorize an execution → deliver that authorization → handler runs → report outcome → conditionally persist outcome → query durable result. Architect must define which steps are needed in M2 and which component invokes each step.
FAILURE: distinguish rejection before acceptance, accepted request with unknown response, authorized execution with uncertain delivery, handler effect with unknown completion acceptance and stale ownership after recovery.
TRADE-OFF: a deliberately single-executor M2 can defer distributed lease machinery if its limitation is accepted and enforced. A recoverable multi-scheduler/worker M2 needs the minimum authority/duplicate/recovery contract immediately, even when the roadmap lists full recovery in M3. Transport selection may pull minimal M5 durability into M2. No temporary transport is selected here.

## Contract questions for Architect

| ID / hotspot | Decision required | Options and consequence | Minimum answer needed before Core code |
|---|---|---|---|
| C01 / CN-02 | Vocabulary and transition authority | Logical job vs execution vs attempt may have different identities/lifetimes; choose only needed concepts. Proposed Core validates policy, Data applies atomic guarded mutations, SDK reports requests/outcomes; this is behavioral input, not ownership of missing server/shared files. | Actor, preconditions, postconditions, durable write point and result for each allowed/rejected transition; whether dispatch/running are durable states or observations. |
| C02 / CN-03 | Eligibility and atomic claim | Authorize in one guarded storage operation vs transactional lock/read/update. Technology, isolation and exact SQL belong to accepted Data design. A prior candidate scan must not itself confer authority. | Due/availability predicates, competitor outcome, returned identity/authority, contention response and transaction boundary; no external dispatch within an open DB transaction. |
| C03 / CN-03 | Claim vs dispatch and delivery acceptance | Worker pull vs scheduler push/durable delivery are alternatives. Claim commit can precede uncertain delivery; transport ACK need not mean execution or durable completion. | Who claims, who delivers, how worker validates authorization, dispatch failure recovery, lost response handling and durable event intent if required. |
| C04 / CN-02/04 | Identity, version and clock | Separate stable job/request correlation, execution/attempt identity and changing authority/version where needed. Storage timestamp vs scheduler wall clock vs a design-defined source affects expiry decisions. | Token scope/generation/check sites, which operations invalidate old tokens, authoritative time source, due/expiry boundary (= vs >), clock skew assumptions and test clock seam. |
| C05 / CN-04 | Renew, expire and recover | Conditional renewal/recovery with monotonic authority is an option; fixed ownership with no recovery is a narrower M2 option. Worker pause may permit old and new handlers to overlap. | Acceptance of late renewal, recovery arbitration, restart discovery, current-owner completion checks and stated overlap/side-effect limitation. |
| C06 / CN-02/04 | Completion and duplicate report | Repeat identical report can return prior acceptance; conflicting reports can reject. Exact response/error mapping is an Architect/SDK public-contract decision. | Guard against obsolete ownership/attempt, dedup scope, conflicting payload behavior, outcome retention and what query says when completion response is lost. |
| C07 / CN-05 | Retry vs handler failure | Terminal first failure is a possible M2 limitation; automatic retry adds durable eligibility/attempt accounting and duplicate exposure. | Failure classification, increment point, retry exhaustion, next eligibility persistence, which failures consume an attempt, and minimum history. |
| C08 / CN-05 | Timeout/cancel vs completion | Guarded first valid transition wins vs explicit priority at a specified linearization point. A durable cancel request and confirmed terminal cancellation may be different concepts. | Winner at simultaneous deadlines, late report treatment, whether stop is best effort, cleanup responsibility and side-effect caveat. |
| C09 / CN-03/05 | Scheduler scan and capacity | Batch/poll choices affect starvation and contention; priority/fairness/backpressure should be scoped rather than invented. | Bounded claim vs worker capacity, pending-delivery limit, eligibility recheck, delayed-work restart and whether priority/fairness is in M2. |

For C02/C04/C05/C06 request logical result categories (accepted/current result, contention/not eligible, stale authority, invalid transition, uncertain outcome) before choosing exception classes or API signatures. Uncertain network response is not proof of transaction rollback; callers need reconciliation rules.

## Proposed race/crash acceptance matrix

All expected behavior below is a proposed invariant for Architect acceptance. It is not existing test evidence. Reliability owns cross-module tests; Core owns future assigned colocated unit tests. Tests NOT_RUN; executable paths/commands/environment TBD after M0/M1.

| Case / milestone | Deterministic timeline | Proposed observation/acceptance question | Needed seam/infrastructure |
|---|---|---|---|
| T01 / M2 distributed claim or M3 | Two schedulers read eligibility, barrier releases both claims. | At most one current authorization for the same eligible execution; loser receives a defined result. This does not assert no later handler overlap. | Controlled interleaving; accepted real storage/isolation for atomicity. |
| T02 / M2 | Pause after durable claim, crash before delivery; restart. | Committed work is eventually reconciled under a defined policy; no unreachable indefinite claim. If M2 omits recovery, limitation and explicit recovery/runbook expectation must be accepted. | Crash hook; storage restart/read-back; transport if selected. |
| T03 / M2 | Deliver same authorization twice, or lose delivery ACK after worker receives it. | State cannot acquire conflicting outcomes; Architect must define duplicate execution policy and worker dedup scope. Do not infer side-effect dedup from state guard. | Repeatable duplicate injection; SDK worker and transport contract. |
| T04 / M2 | Persist accepted completion, lose response, resend identical then conflicting report. | Defined replay response; conflicting report cannot overwrite accepted result; query reconciles uncertainty. | Completion commit/response hook; durable read-back. |
| T05 / M3 (M2 if recovery included) | A pauses, authority expires, B acquires new authority, A reports/renews. | Old authority cannot mutate current durable state; separately observe whether external effect repeats and document that limitation. | Advance authoritative test time; two workers; real storage; instrumented effect. |
| T06 / M3 | Barrier renewal and recovery at exact expiry boundary. | One consistent accepted authority per design; renewal/expiry comparison and stale response are explicit. | Shared controlled clock and guarded mutation races. |
| T07 / M4 (M2 if retries included) | Accept failure/retry scheduling, crash before next attempt; replay failure event after restart. | Retry eligibility survives; repeat failure does not double-count attempts or schedule multiple current retries. | Durable retry state; restart and duplicate report hooks. |
| T08 / M4 | Barrier completion vs timeout/cancel; retry stale completion after winner. | Exactly the design-defined durable outcome is accepted; late report cannot undo it. Handler/effect stopping remains separately specified. | Time and mutation barriers; unit policy plus storage integration tests. |
| T09 / M2 | Reject invalid payload/unregistered handler; distinguish error detected at submit vs worker execution. | No accepted work on submit rejection; accepted work that later finds unknown handler has an explicit failure outcome. No silent abandonment. | Validator/registration policy seam; SDK boundary. |
| T10 / M2 basic delay or M4 advanced schedule | Restart around due time; replay scan/claim at the equality boundary. | No authorization earlier than accepted clock eligibility; eventual execution depends on documented availability/capacity assumptions. | Controllable time; durable restart; bounded claim capacity. |

Core unit targets after assignment: transition precondition/result tables, stale authority policy, eligibility boundary and retry/timeout arbitration under a fake clock. A unit fake cannot prove storage atomicity; T01 and cross-process failure windows need accepted real infrastructure tests with Reliability/Data.

## Concrete handoff requests and decisions

Architect: answer C01–C09 in M1-DESIGN-001, link accepted design/ADR evidence when available, choose M2 guarantee scope and include needed failure timelines. Return transition table + Core/Data/SDK logical operations with preconditions, persistence/linearization points, response semantics and accepted test outcomes. Draft answers are useful input but do not authorize implementation.
Data: through Architect's contract, identify atomic claim/renew/complete/recover capability and transaction boundaries; cover intent/delivery windows if push messaging is chosen. Do not presume a selected DB, transport, schema or repository signature.
SDK: through accepted contract, define worker authorization handling, completion retry/reconciliation and shutdown/cancellation behavior; do not expose internal scheduler/storage tokens without public boundary review.
Reliability: align T01–T10 with its matrix; keep exact expected outcomes TBD until design acceptance. Instrument state authority separately from external effect counts.
Coordinator: collect this publication evidence, resolve assignment dependencies, schedule Core code only after accepted M1/M0 and exact code paths. Do not label preparation as integrated DONE.

Owner decisions needed: accept Architect's concrete M2 scope (single vs distributed/recoverable execution; minimum retry/cancel/scheduling), architecture/dependency/public-contract proposals, exact missing path mapping, and resolve developer/develop + Release activation. This handoff requests those concrete design proposals; it asks Owner to approve no invented implementation today.

## CN hotspots for Owner notebook (TODO; no notebook edits)

| ID | Invariant to explain after acceptance | Future code evidence to locate | Failure/test reference and trade-off |
|---|---|---|---|
| CN-02 | Only valid, authorized transitions change durable lifecycle. | Transition validation and guarded completion symbols; path/symbol/SHA TODO. | C01/C06, T04/T09; simple lifecycle vs richer execution/attempt model. |
| CN-03 | Eligibility discovery is distinct from atomic authorization. | Claim predicate, transaction and dispatch boundary; path/symbol/SHA TODO. | C02/C03, T01/T02; contention/throughput vs correctness and recovery complexity. |
| CN-04 | Obsolete authority cannot overwrite current state; external effects need their own contract. | Token check at renew/complete/recover, authoritative clock; path/symbol/SHA TODO. | C04/C05, T05/T06; faster recovery vs possible execution overlap. |
| CN-05 | Retry/timeout/cancel follow one accepted arbitration and attempt policy. | Attempt accounting, next eligibility and terminal guard; path/symbol/SHA TODO. | C07/C08, T07/T08; recovery/retry availability vs duplicate effects. |

60-second draft explanation (conceptual, not code evidence): scheduler discovery finds candidates but must obtain durable authorization before execution. A worker reports with the identity/authority the accepted contract requires, and completion is accepted conditionally. If an old worker returns after recovery, its state update must be handled by the chosen stale-authority policy; its external effect needs separate protection. We will verify the distinction with controlled claim/renew/completion races and restart tests. Current code/test evidence remains TODO/NOT_RUN.

## Blockers / next action / acceptance evidence

Preparation output is complete: immutable sources ACKed, questions/options C01–C09, proposed failure matrix T01–T10, CN-02/03/04/05 and Owner decision needs recorded. No accepted guarantee, schema, dependency, signature, test pass or architecture decision is asserted.
Production blockers: accepted design/public boundary, exact missing path ownership, build/test foundation and implementation assignment. Integration blockers: developer/develop decision, Release availability and exact-candidate Reviewer gates.
Next safe action: publish Core-owned status/handoff for Coordinator and Architect to read by SHA; consume design response when published. No tool messages to other chats, no shared integration, no production writes.
