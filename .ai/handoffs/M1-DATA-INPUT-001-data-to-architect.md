# DATA-M1-001 — Persistence and messaging inputs for Architect

author: data
task_id: M1-DATA-INPUT-001
message_id: DATA-M1-001
recipient: architect; coordinator (assignment/evidence tracking)
status: PREPARATION_COMPLETE; PROPOSED_INPUT; NOT_ACCEPTED_DESIGN
updated_at: 2026-10-03 20:04 +07:00 (Asia/Saigon)
source_branch: agent/data/m1-input
worktree: E:\Github project\Chronos-worktrees\data-m1-input
implementation_sha: none

## ACK and immutable sources

ACK COORD-ROAD-001 at 4a18310a0af1f982ab326b77c496b4721fd8ef18, specifically .ai/handoffs/COORD-ROAD-001-coordinator-to-agents.md and .ai/context/TASK_BOARD.md. Assignment: analysis only, status and author-created handoff/proposal only. No schema, SQL, migration, production code or executable test is assigned.

- Rules/prompt/workflows: 9a45b111847685255cf1006a2b839d31ecdf31a4:AGENTS.md, .ai/rules/{collaboration,git,coding}.md, .ai/prompts/data.md, .ai/workflows/{recovery,feature}.md.
- Operational context/decisions/risks and Coordinator status: 4a18310a0af1f982ab326b77c496b4721fd8ef18:.ai/context/{PROJECT_CONTEXT,CURRENT_STATE,TASK_BOARD,DECISIONS,RISKS}.md and .ai/status/coordinator.md.
- Planning/notebook: 2ce8a98f810cd00d268332ad68df45fc8f99a866:ROADMAP.md and CODE_NOTES.md, especially M1–M5 and CN-03/06/07/08. These are planning sources, not accepted contracts.

No accepted architecture/ADR or implementation exists in the assigned baseline. Origin and Git now exist; earlier pre-Git statements in this chat are obsolete. Canonical checkout belongs to Prompt Master. Worktree/branch inventory showed no Data checkout/branch before creation; assignment targets this Data chat. No other checkout was switched. Files in another role's branch are read by immutable revision, never merged for communication.

## WHY / WHAT / FLOW / FAILURE / TRADE-OFF

WHY: a successful submit, a durable ownership decision and a broker acknowledgement are different facts. The design must specify what can be recovered after each interruption before Data can implement it.
WHAT: request observable repository capabilities and explicit transaction/transport contracts, without inventing entities, status names, public signatures or tables.
FLOW (proposed outline): validate input -> persist accepted intent -> acquire eligible work -> dispatch/execute outside the database transaction -> conditionally record outcome -> query outcome. If events are selected, persist event intent with its associated state change, then publish outside that transaction under a separately accepted recovery contract.
FAILURE: lost response can hide a successful commit; an uncertain publish can be retried and duplicated; expired ownership can leave a previous handler running. The matrix below supplies design questions, not guarantees already implemented.
TRADE-OFF: reducing moving parts may simplify M2, but cannot erase a required durability boundary. Broker/outbox/dedup/fencing each solve a specific boundary and add operations, retention and testing costs. Architect should justify the minimum accepted contract rather than mechanically postponing all messaging until M5.

## Repository and transaction requirements to decide

These are capability questions, not proposed API signatures or schema definitions.

| Capability | Required design decision / observable contract |
|---|---|
| Accept submission | What is the acceptance/commit point? Which validation occurs before it? Can unknown handlers be durably accepted? How does an ambiguous commit reach the client? |
| Resolve repeated submission | If idempotent submit is selected, resolve concurrent same-key requests atomically; define identity scope, canonical payload comparison, expiry and same-key/different-payload behavior. Return the same accepted identity or a specified conflict, not an invented outcome. |
| Find/acquire eligible work | Eligibility predicate, clock source, ordering/fairness, batch bound and atomic ownership grant. Specify what competing callers observe when acquisition loses. Discovery alone must not authorize execution. |
| Renew/finish/recover ownership | Define allowed transitions and preconditions checked atomically with the write: current ownership identity/version, lease validity and domain state as applicable. Return a distinguishable rejected/stale/conflict outcome. Lease policy belongs to accepted Core/Architect design. |
| Record handler failure/outcome | Decide attempt identity, durable outcome/history and how duplicate or contradictory reports resolve. Include retry/timeout/cancel contention when those milestones are enabled. |
| Query outcome | Consistency/read-your-writes requirement, missing vs unavailable result, payload limits, pagination and access scope. A stale replica result must not be silently treated as proof of nonexistence. |
| Persist/recover event intent (conditional) | Decide which state transitions require events, atomicity boundary, event identity/version, recovery scan and publisher progress semantics. No event table is prescribed here. |
| Record consumer progress (conditional) | Where can dedup and internal effects commit together? Which ACK represents broker acceptance vs application completion? What external effects cannot join that transaction? |

Transaction policy already required by coding.md: do not keep a database transaction open across broker/network/handler calls. Architect must identify the durable evidence bridging each call and the recovery actor. Isolation/lock/conflict retry policy, bounded waits and clock authority remain TBD. A check followed by a separate unconditional write cannot establish exclusive acquisition; the eligibility/ownership decision must have an atomic enforcement point chosen in design.

Atomic claim options for evaluation, with no selection: conditional write/version check (losers need explicit conflict behavior and retry bounds); transactional locking/acquisition supported by the selected store (lock duration, contention and ordering need validation); serialized authority with durable recovery (coordination dependency and failover cost). Compatibility with the chosen storage and actual throughput/fairness target must decide the mechanism. A database lock or ownership token does not by itself constrain a handler's external side effect.

## Failure matrix — expected behavior is proposed / TBD pending acceptance

All rows are design/test targets; executable tests NOT_RUN. Reliability owns cross-module failure tests when assigned. Liveness expectations below require services to recover and retry within an accepted operational policy.

| ID / milestone | Interruption / race | Proposed expectation and unresolved decision | Evidence to plan |
|---|---|---|---|
| D01 / M2 | Invalid input before accept | No executable intent for rejected input; unknown-handler policy TBD. | Durable state and client error after invalid/unknown input. |
| D02 / M2 | Crash before submission transaction commits | No partially accepted submission; retry follows accepted dedup policy. | Restart inspection at commit boundary; rollback vs commit outcome. |
| D03 / M2 | Commit succeeds, submit response is lost | Do not assume submission failed. If dedup is promised, retry resolves the same accepted identity; key/payload rules TBD. | Drop response after commit, retry, inspect identity/count/outcome. |
| D04 / M2 | Two same-key submissions race; payload equal or conflicting | One accepted intent per defined dedup scope if selected; conflicting payload outcome explicitly specified. | Synchronize requests; assert durable resolution and distinct conflict case. |
| D05 / M2–M3 | Two schedulers attempt the same eligible work | One ownership grant for the same generation; loser cannot dispatch based on earlier discovery. | Controlled acquisition interleaving and durable grant evidence. |
| D06 / M3 (earlier if M2 promises recovery) | Claim commits, dispatcher crashes before delivery | Accepted intent remains recoverable; define lease/recovery actor and eligibility. No permanent disappearance. | Kill after claim, restart/recover, observe generation and eventual dispatch. |
| D07 / M3 | Worker A pauses, ownership expires, B acquires, A completes/renews | Stale internal writes rejected under chosen ownership contract. A's external effect may still run; downstream fencing/idempotency TBD. | Gate A/B operations and inspect conditional-write result and effects separately. |
| D08 / M1–M2 if broker required; otherwise M5 | State and event intent transaction fails before commit | If transactional intent is selected, both commit or neither does. | Fault at transaction boundary; state/intent correspondence. |
| D09 / same | State+intent commit, crash before publish | Durable intent recoverable after restart; define publisher scan/progress and retry. | Restart without in-memory signal; observe pending intent delivery. |
| D10 / same | Broker receives event; publish ACK is lost or progress not stored | Retry may duplicate event. Stable logical identity and consumer handling required if promised. | Inject after acceptance/before progress; observe duplicate and durable effect count. |
| D11 / same | Consumer crashes before internal effect commits | With effect+dedup in one transaction, neither partial effect nor completed marker; redelivery can proceed. | Abort transaction, redeliver, inspect marker/effect together. |
| D12 / same | Internal effect commits, crash before consumer ACK/offset | Redelivery may occur; accepted dedup scope prevents repeating protected internal effect if selected. | Drop ACK, redeliver, inspect durable effects and progress. |
| D13 / M5 or first external-effect execution | External effect succeeds, crash before recording success | Local dedup alone cannot prove external effect occurred once. Downstream idempotency/fencing or explicit duplicate/uncertain-result limitation needed. | Separate external destination observations from job outcome; retry after ambiguous response. |
| D14 / M5 | ACK/offset advanced before durable handling | Possible loss on crash: specify that advancement follows the selected durable handoff/effect boundary; async handoff must itself be durable. | Crash after advancement, before work; prove recoverability or reject unsafe order. |
| D15 / M5 | Out-of-order event, poison input, DLQ/redrive | Ordering scope, stale-event policy, bounded retries, durable quarantine and replay identity/authorization TBD; avoid poison event blocking forever. | Reorder deliveries, exhaust failure policy, restart and controlled replay. |
| D16 / M2–M5 | DB/broker outage or commit result unknown | No false success/rejection; distinguish known rollback from uncertain acceptance. Retry bounds/backoff and observability TBD. | Fail connections around commit/publish; reconcile durable evidence after recovery. |

D05 protects acquisition; it does not mean executions can never overlap after expiry. D10/D12 protect defined internal effects; they do not imply D13 exactly-once business effects. Timeout/cancel/retry races should reuse the same conditional mutation boundary once Architect accepts those transitions, not introduce independent state authorities.

## Persistence and transport options for Architect + Owner

| Option to evaluate | Benefit | Cost / contract that must be explicit |
|---|---|---|
| Durable store with direct pull/poll for first slice | Fewer failure boundaries and dependencies. | Discovery latency, contention, wakeup/fairness and worker interface; accept only if it meets first-job requirements. |
| Durable store + broker + transactional intent publisher | Separates durable acceptance from dispatch and supports restart discovery. | Duplicate delivery, publisher coordination, retention, extra operations and consumer progress policy. Does not alone protect external effects. |
| Store + recoverable publication via change stream | Can derive publication from committed changes. | Capture/retention/replay lifecycle, event contract and deployment/version compatibility; assess whether required infrastructure is justified. |
| Broker as initial durable acceptance boundary | May align with transport-first processing. | Reconcile queryable state, dedup and acceptance semantics with broker durability; not interchangeable with atomic DB acceptance without a design. |

No database, broker, Java/build/dependency version, isolation level or delivery guarantee is selected. PostgreSQL/Kafka named in planning remain candidates. Keep M5 topics as questions now; move only the minimum into M2 if accepted design actually needs them.

## Query/index and migration inputs

No SQL or index definition is proposed without the accepted schema and query workload. Gather these query purposes: scoped submit-key resolution; eligible-work acquisition; expired-ownership recovery; outcome lookup; pending-publication recovery if selected; retention cleanup/history pagination if in scope. For each actual query later record filters, sort/tie-break/pagination, columns/selectivity/cardinality, execution plan on representative data, read benefit, write/storage cost and contention. High-update ownership fields and cleanup must be included in the write-cost analysis. Performance results remain UNVERIFIED.

Migration decisions needed: version/tool owner, initial schema assignment, supported mixed-version deployment window, event/payload version compatibility, expand/backfill/contract order where needed, concurrent writers during backfill, failure recovery, lock/runtime budget and rollback vs forward-fix when data cannot be reversed. Released migrations stay immutable. Tests later need empty install, upgrade from supported predecessor, restart after interrupted migration and supported reader/writer combinations. No migration exists or was run in this task.

Dedup/event/history retention must be coordinated: deleting a dedup record too early can turn delayed retries/redrive into new work. Define expiry behavior and duplicate protection horizon relative to transport retention, outage and replay windows; do not choose durations without requirements.

## Requested Architect response and Owner decisions

1. Define M2 acceptance boundary and repository mutation preconditions; identify durable recovery actor at every external call boundary (D02–D07).
2. Specify submit idempotency scope/key/canonical comparison/conflict/expiry, and client retry behavior after ambiguous acceptance (D03–D04). SDK owns public error/signature implementation after approval.
3. Decide store/transport strategy and whether broker/intent/dedup is needed in M2 or later; document alternatives and versions only after validation (D08–D16).
4. Define identity/version/clock/lease rules and the exact scope of stale-owner rejection; state downstream side-effect limitations separately (D05–D07/D13).
5. If events are selected: identify producers/consumers, identity/schema version, ordering scope/key, retention, broker ACK vs processing completion, retry/quarantine/redrive and progress boundary.
6. Provide accepted minimal domain/serialization/access constraints and actual query purposes/workload so schema/index design can be assigned rather than inferred.
7. Decide migration/compatibility envelope and infrastructure/build/test foundation. Coordinator must assign exact implementation paths and acceptance only after design acceptance.

Architect drafts the contracts/ADRs. Owner acceptance is required for database architecture, transport/concurrency strategy, dependencies and public API choices under existing rules. Separate M0 decisions already tracked by Coordinator: developer/develop naming, Release activation and missing ownership mapping. This handoff does not grant new ownership or approve a technology. No immediate Owner confirmation is needed to publish this analysis.

## CN hotspots / learning and acceptance evidence

| Notebook ID | Proposed focus | Invariant / timeline / trade-off | Code/test evidence |
|---|---|---|---|
| CN-03 | Acceptance and atomic acquisition | D02/D05/D07; atomic preconditions vs locking/conditional-write contention. | Path/symbol+implementation SHA TODO; tests NOT_RUN. |
| CN-06 | Submit vs event vs business-effect idempotency | D03/D04/D12/D13; retention/conflict scope vs storage and downstream cooperation. | Path/symbol+implementation SHA TODO; tests NOT_RUN. |
| CN-07 | Durable intent and publisher recovery | D08–D10; coupled local commit vs duplicate publish and extra operations. | Path/symbol+implementation SHA TODO; tests NOT_RUN. |
| CN-08 | Transport progress, ordering and replay | D12/D14/D15; durable completion boundary vs lag, poison events and replay risk. | Path/symbol+implementation SHA TODO; tests NOT_RUN. |

60-second preparation explanation: a lost submit response does not tell us whether acceptance committed. Acquisition must atomically enforce current eligibility, while an expired lease may still leave an old handler running. Durable event intent can bridge commit-to-publish recovery but publish/ACK ambiguity can produce duplicates. Every protection needs a named scope and observable failure test; the current task supplies questions and timelines only. No existing code or test proves a Chronos guarantee yet. Owner's CODE_NOTES.md was not edited.

Acceptance self-check: assignment ACK and source revisions recorded; repository/transaction/claim inputs supplied; duplicate submit/lost response and commit/publish/ACK windows enumerated; query/index/migration compatibility requirements and technology alternatives supplied; Architect questions and Owner decisions separated. Preparation is complete; review approval, integration and production readiness are not claimed.
