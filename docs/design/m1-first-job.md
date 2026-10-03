# M1 first job — proposed domain and contracts

author: architect
task_id: M1-DESIGN-001
status: DRAFT_FOR_OWNER_DECISION
updated_at: 2026-10-03 20:10 +07:00 (Asia/Saigon)
source_branch: agent/architecture/m1-foundation
references: COORD-ROAD-001 at 4a18310a0af1f982ab326b77c496b4721fd8ef18; [foundation](../architecture/m1-foundation.md); [decision proposal](../../.ai/proposals/ARCH-PROP-001-m1-foundation.md)

All field names, states, wire operations, numeric limits and semantics below are proposed. No public Java signature is accepted; no code/schema/test exists. This document defines testable behavior for review, not storage DDL.

## WHY / WHAT — minimal domain

Job is a durable request: server job identity, authenticated scope, submit idempotency key, immutable task name/schema/payload, accepted time and outcome. Attempt is one grant of permission to execute that job: identity, increasing job-local generation, worker principal/session, lease deadline and recorded disposition. A job may need several attempts after loss of ownership; a separate execution entity adds no demonstrated M2 behavior and is deferred. Worker is a client process with a fresh session identity and local handlers, not an additional business entity/table by default. Server task catalog is configured by the operator, independently of temporary worker liveness; no worker available means queued, not invalid submit.

Proposed Job states: QUEUED, RUNNING, SUCCEEDED, FAILED. Attempt dispositions: ACTIVE, SUCCEEDED, FAILED, EXPIRED. FAILED records structured cause and whether effects are uncertain; it never proves no external effect occurred. No TIMEOUT/CANCELLED state is added before the M4 race design. Lease expiry is loss of ownership, not business timeout.

| Transition | Trigger / atomic requirements |
|---|---|
| none → QUEUED | Valid authorized submit and unique scoped key; job + key receipt commit together |
| QUEUED → RUNNING | One atomic eligible claim, new generation/attempt and claim receipt committed together |
| RUNNING → SUCCEEDED | Current ACTIVE attempt, matching principal/session/token and unexpired lease; persist bounded result and close attempt together |
| RUNNING → FAILED | Same ownership checks; handler/codec error closes attempt; M2 does not retry business errors |
| RUNNING → QUEUED | Expired ACTIVE attempt closed EXPIRED and job requeued in one transaction, within recovery budget |
| RUNNING → FAILED | Expired attempt, recovery budget exhausted; error RECOVERY_EXHAUSTED with effectsUncertain=true |
| terminal → same terminal | Exact replay of accepted completion returns previous receipt; never executes handler or changes result |

## Proposed invariants

- **I1 Durable identity:** one job per `(authenticated scope, submit key)` for the documented retention window. Same immutable command replays same job; different command conflicts.
- **I2 Valid lifecycle:** only named transitions occur; immutable job input and terminal outcome do not change through replay/recovery.
- **I3 Atomic ownership:** at most one ACTIVE current attempt stored per job; claiming and setting RUNNING cannot partially commit. Concurrent workers may not both receive distinct valid current grants.
- **I4 Fenced writes:** renew/complete require current generation/token, authorized worker session and lease validity checked after acquiring relevant lock. Recovery and completion serialize on the same job/attempt. Stale writes cannot overwrite newer state.
- **I5 Receipt replay:** claim/completion response loss must not create an additional attempt or overwrite an outcome. Replayed retired claim never yields a fresh runnable assignment.
- **I6 Short transactions:** no handler, HTTP, broker or downstream call inside storage transactions. DB time controls eligibility; worker time does not grant authority.
- **I7 Bounded recovery:** expiry can schedule another attempt within a declared limit; execution may overlap or repeat. Downstream idempotency/fencing is the handler owner's responsibility.
- **I8 Boundary/security:** scope comes from authentication, not caller-provided tenant identity; SDK contains no storage/Kafka/server classes; only configured task codecs execute.

I3 describes stored ownership, not a guarantee that only one process executes user code. Attempt identity/generation is an opaque internal ownership credential on the wire, not a public persistence row.

## FLOW — transaction contracts Core ↔ Data

These are semantic operations, not Java signatures. Core owns policies/transitions; Data supplies indivisible persistence operations with observable outcomes. The calling use case must not implement read-then-unconditional-write around these ports.

| Operation | Inputs / result | Transaction and error semantics |
|---|---|---|
| SubmitOrReplay | Auth scope, key, validated immutable command → created/replayed job receipt or key conflict | Persist job + scoped key + fingerprint atomically. Unique conflict rereads winner and compares command. Response only after commit |
| ReadJob | Authorized scope/job → public snapshot or not found | Consistent committed snapshot including matching outcome/current attempt summary; no replica-lag guarantee proposed |
| ClaimOrReplay | Worker principal/session, acquisition key, exact supported task/schema set, capacity=1 → assignment, empty or retired/conflict | Serialize scoped acquisition key; eligible queued job claim + increment generation + attempt + receipt atomically. Empty result is also remembered. A new poll uses a new key |
| Renew | Attempt/token/session + renewal request key → stored deadline or stale/conflict | Lock current job/attempt, obtain DB time after lock wait, verify ACTIVE/unexpired and owner, extend to monotonic configured deadline; request replay returns original renewal receipt |
| CompleteOrReplay | Attempt/token/session + completion request key + disposition/result/error fingerprint → accepted, replay, stale or conflict | Authenticate original authorized principal/session/credential before lookup; replay already accepted matching receipt even if its old lease has since expired. Otherwise lock/check current unexpired owner, atomically close attempt + terminal job + receipt; terminal job with a different completion key cannot change outcome |
| RecoverExpired | Bounded batch → expired/requeued/exhausted counts | Lock each candidate and recheck expiry/current generation with DB time; close attempt and queue/fail job together. Restart reruns scan, no in-memory checkpoint required |

Lock order: job first, then current attempt/receipt as needed; Data must document unique-key conflict handling and deadlock retries. Acquisition-key arbitration must precede selecting work, so duplicate HTTP calls cannot claim different jobs with the same key. Limit transaction duration; never use a transaction-start timestamp that is stale after a long lock wait to authorize renew/complete. Lease renewal and completion evaluate a precise DB-clock validity boundary after lock acquisition; later commits may be delayed while conflicting recovery waits. This is the proposed linearization point, not a wall-clock promise about handler execution.

Recommend PostgreSQL conditional updates/row locks with READ COMMITTED plus unique constraints; actual SQL and constraint design are Data work after acceptance. SKIP LOCKED is a queue contention option and supplies neither strict FIFO nor a generally consistent query view ([PostgreSQL locking clause](https://www.postgresql.org/docs/18/sql-select.html#SQL-FOR-UPDATE-SHARE)). A READ COMMITTED statement may observe committed changes; check predicates under locking/conditional writes rather than assuming a prior SELECT remains valid ([transaction isolation](https://www.postgresql.org/docs/18/transaction-iso.html)). Other isolation strategies are possible but need retry contracts and real DB evidence.

Logical access patterns before index/DDL choice:

| Access | Proposed index candidate / justification / cost |
|---|---|
| Submit dedup | Unique scope + key; identity arbitration for selective equality query; extra insert/storage cost |
| Eligible immediate task | Task/schema + accepted time + job ID over QUEUED subset; filter/order limited claim; selectivity depends on queue and task mix, write cost on queue entry/exit |
| Recovery | Lease deadline + job/attempt identity over ACTIVE subset; range scan bounded expired work; heartbeat updates cause index write amplification |
| Query/replay | Job/attempt identity and scoped request key uniqueness; exact lookup; receipt retention adds storage and cleanup cost |

These are candidates, not asserted query plans. Data must provide exact queries, columns, constraints, selectivity estimates and write costs; Reliability measures actual plans/workload. Migrations must support rolling compatibility and backfill, with released migrations immutable.

## Proposed SDK ↔ server wire contract

Use versioned HTTP JSON with typed errors; SDK maps to public values without exposing internal tokens through ordinary job-query models. Worker protocol may carry opaque ownership tokens. Operations below are a reviewable v1 proposal, not API approval.

| Operation | Proposed request/response |
|---|---|
| `POST /v1/jobs` | Idempotency-Key; taskName, schemaVersion, payload → jobId/state receipt (201 created, 200 replay) |
| `GET /v1/jobs/{jobId}` | Authorized query → jobId/state, timestamps, bounded result or structured error, effectsUncertain; no raw storage fields |
| `POST /v1/worker/claims` | Acquisition key, fresh worker session, supported task/schema pairs, capacity=1 → assignment or explicit empty (200); immutable job data, attemptId, opaque generation credential, deadline |
| `POST /v1/worker/attempts/{attemptId}/renew` | Renewal key + owner credential → committed lease deadline |
| `POST /v1/worker/attempts/{attemptId}/complete` | Completion key + credential + success/result or failure/error → committed disposition receipt |

Proposed errors: 400 malformed envelope/key/JSON; 401 unauthenticated; 403 forbidden scope/capability; 404 job absent or outside visible scope; 409 key conflict, retired claim or stale ownership (distinct stable error codes); 422 unknown configured task/schema or schema-invalid payload; 413 oversized body; 429 bounded admission pressure; 503 transient store/service unavailable. Error envelope: code, safeMessage, requestId, retryable; no SQL, stack trace, secrets or arbitrary exception serialization. Transport timeout/disconnect is an unknown outcome, not confirmed rejection. Automatic SDK retries use the same operation key and bounded backoff/deadline; never resubmit with a new key automatically. Explicit business errors are not transport retry signals.

Command equality: validate UTF-8 JSON, reject duplicate keys/non-finite numbers, canonicalize object key order, preserve array order and exact accepted numeric values; include task/schema and all behavior-changing fields. Core specifies canonical format and Data persists normalized command + digest with version. SDK does not invent the server fingerprint. Equivalent formatting should replay; changed payload/schema should conflict. Canonicalization version cannot change existing-key equality silently during upgrade. Worker result/error replay uses the same normalization contract. No Java class names/default polymorphic deserialization. Handler registry uses `(taskName, schemaVersion)` and explicit codecs; registration is local, workers cannot mutate server allowlist by claiming support.

Proposed initial configurable bounds for Owner decision: payload/result 64 KiB each, safe error 4 KiB, key 128 UTF-8 bytes, one assignment per claim, lease 30 seconds, renewal every 10 seconds, at most 3 total ownership attempts per job, HTTP request deadline 5 seconds. These are starter policy values, not performance measurements. Lease timings must be validated against pauses/network/storage latency. Worker validates encoded size/schema before invoking handler; serialization/result error becomes structured failure if ownership still valid. If renewal fails or lease validity is uncertain, stop taking work, attempt cooperative handler interruption and do not assume external side effects stopped.

Receipt/key retention proposed: job/outcome and linked submit/acquisition/completion receipts retained 7 days after terminal outcome; never delete active-job receipts. Empty acquisition receipts are retained 7 days from creation and advertise their own dedupUntil. Expose dedupUntil on terminal query/receipt. Clients may replay during the advertised window; after purge, job may be 404 and a reused submit key may create a new job. Retired/empty acquisition replay must not grant new work; expired receipt keys return an expiry error if a tombstone is retained, otherwise reuse outside the window has no dedup guarantee. Renewal receipts can be compacted after retirement only when all further renewals return stale and cannot change state. Cleanup cannot remove idempotency evidence from active jobs. Retention values/GC/tombstone policy require Owner/Data agreement before implementation.

## Worker lifecycle and side effects

Authenticate, create fresh session, build local codec/handler map, claim only with a free executor slot, verify assignment compatibility, invoke handler outside transaction, renew on an independent bounded timer, encode outcome, complete with stable request key and retry the receipt if response was lost. Process assignment at most once per attempt in that live session; replayed claim does not schedule a second local invocation. Restart uses a new session and does not resume a prior token blindly. Shutdown stops claiming, keeps renewal while draining within configured grace, then cooperatively interrupts; leftover jobs recover through expiry. Handler exceptions are FAILED without automatic business retry in M2.

JobId can be a stable downstream business-effect key across ownership attempts; attemptId is unsuitable for deduplicating the same business effect across recovery. A downstream store must implement its own durable idempotency/transaction or enforce a fencing generation if such a guarantee is needed. Lease token alone cannot undo an email/payment already sent. An expired attempt marks effectsUncertain conservatively even if actual handler start was unknown; retain this marker/history across requeue and later terminal outcomes so a later success does not imply prior effects were absent. No universal exactly-once, strict FIFO, unlimited retry, guaranteed eventual completion or finite handler-stop guarantee is proposed.

## FAILURE — deterministic timelines and acceptance expectations

All tests below are planned / NOT_RUN. Real DB required for transaction/race evidence; transport fault injection and controlled worker processes required for delivery/restart. No sleeping-only race test or substituted mock DB proves these invariants.

| ID / invariant | Timeline / observation | Proposed expected outcome / infrastructure |
|---|---|---|
| F01 I1 | Two submits same scope/key, barrier before writes; same vs changed command | One job; matching replay same identity; changed command 409; real DB concurrent clients |
| F02 I1/I6 | Submit transaction rollback; then commit followed by dropped response | Rollback leaves neither job nor receipt; same-key retry after commit returns job; DB + HTTP response-drop proxy |
| F03 I3 | Two claimers block at same eligible job; commit winners | One current attempt; loser gets other job/empty, never valid duplicate owner; real DB barriers |
| F04 I3/I5 | Duplicate acquisition key concurrent calls; drop committed claim response | Same receipt/attempt, no extra job claimed; retired replay cannot execute; DB + worker invocation counter |
| F05 I4/I7 | A pauses, lease expires, recovery requeues, B claims; A resumes renew/complete | A stale writes rejected; B state intact; external effect can overlap, demonstrate with an idempotent sink and non-idempotent counter |
| F06 I4 | Complete vs recover at expiry; renewal lock waits across deadline | Exactly one serialized transition wins; waiting stale renewal fails; DB-clock boundaries/barriers |
| F07 I2/I5 | Complete committed, response dropped; matching and changed replays | Matching returns receipt even after original deadline; changed replay 409, terminal result immutable |
| F08 I6/I7 | Worker crashes before handler; then after external effect before complete | Expire and bounded re-execution; uncertainty preserved and external effect dedup tested; kill/restart worker + real sink |
| F09 I2/I8 | Unknown task, invalid schema/JSON, duplicate JSON keys, oversize, foreign scope | Defined rejection before job write, no class loading/payload leakage; wire/authorization fixtures |
| F10 I2 | Handler throws, codec/result serialization fails, no compatible live worker | Error becomes FAILED while ownership valid; compatible absence stays QUEUED; handler/codec fixture |
| F11 I6 | Store outage during submit/renew/complete; restart server | Unknown client result replayed safely, no in-memory-only queue loss; no transaction held around handler; DB outage injection |
| F12 I7 | Repeated worker crashes to configured budget | Bounded attempts then FAILED/RECOVERY_EXHAUSTED, effectsUncertain=true; controlled expiry/process failures |
| F13 I1/I5 | Cleanup races replay, active job outlives 7 days, canonicalization upgrade | Active evidence retained; advertised retention honored; old fingerprints replay consistently; migration/GC fixture |

Timeout/cancel/priority/business retry and broker commit/publish/ACK tests are future contracts, not fabricated M2 outcomes. If Owner chooses broker delivery, this draft must add durable event intent in the same job transaction, publisher retry/dedup, consumer ACK rules and poison-event disposition before code begins. With pull, there is no publish/ACK window; claim/completion response loss above is the equivalent window to verify. Broker delivery never inherits an exactly-once claim from this design.

## TRADE-OFF / acceptance

Pull avoids a DB/broker dual-write dependency but adds polling latency/load and no strict fairness. Bounded lease recovery prevents permanently lost ownership but permits repeat/overlap and requires idempotent handlers; one attempt with UNKNOWN/manual intervention is the simpler alternative if Owner accepts lost automatic progress. Separate job/attempt makes these limits visible without inventing a full workflow entity model.

Design acceptance needs explicit Owner approval of ARCH-PROP-001 decisions and Core/Data/SDK/Reliability feedback reconciliation. Numeric limits, public operation/error model, retention/canonicalization, persistence/clock semantics and downstream uncertainty are all review points. Coordinator then assigns exact implementation/test paths; Release builds foundation; Reviewer approves exact tested code revisions. No test command, performance number or integration approval exists yet.
