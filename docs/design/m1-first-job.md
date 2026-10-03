# M1 first job — proposed domain and contracts

author: architect
task_id: M1-DESIGN-001
status: DRAFT_FOR_OWNER_DECISION
updated_at: 2026-10-03 21:13 +07:00 (Asia/Saigon)
source_branch: agent/architecture/m1-foundation
references: COORD-ROAD-001 at 4a18310a0af1f982ab326b77c496b4721fd8ef18; [foundation](../architecture/m1-foundation.md); [decision proposal](../../.ai/proposals/ARCH-PROP-001-m1-foundation.md)

All field names, states, wire operations, numeric limits and semantics below are proposed. No public Java signature is accepted; no code/schema/test exists. This document defines testable behavior for review, not storage DDL.

Revision after PM-ARCH-001: full published Core/Data/SDK/Reliability inputs and Reviewer/Release/Coordinator evidence were reconciled. Immutable source manifest and per-input dispositions live in [ARCH-PROP-001](../../.ai/proposals/ARCH-PROP-001-m1-foundation.md), the single synthesis record. Input publication is resolved by PM-PUB-001; acceptance/runtime evidence remains pending.

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
| SubmitOrReplay | Auth scope, key, normalized immutable command → created/replayed job receipt or key conflict | Authenticate and parse/bound command, resolve existing scoped receipt/equality first; new acceptance requires configured task/schema validation. Persist job + scoped key + fingerprint atomically. Unique conflict rereads winner and compares command. Response only after commit |
| ReadJob | Authorized scope/job → public snapshot or not found | Consistent committed snapshot including matching outcome/current attempt summary; no replica-lag guarantee proposed |
| ClaimOrReplay | Worker principal/session, acquisition key, exact supported task/schema set, capacity=1 → assignment, empty or retired/conflict | Serialize scoped acquisition key; eligible queued job claim + increment generation + attempt + receipt atomically. Empty result is also remembered. A new poll uses a new key |
| Renew | Attempt/token/session + renewal request key → stored deadline or stale/conflict | Lock current job/attempt, obtain DB time after lock wait, verify ACTIVE/unexpired and owner, extend to monotonic configured deadline; request replay returns original renewal receipt |
| CompleteOrReplay | Attempt/token/session + completion request key + disposition/result/error fingerprint → accepted, replay, stale or conflict | Authenticate original authorized principal/session/credential before lookup; replay already accepted matching receipt even if its old lease has since expired. Otherwise lock/check current unexpired owner, atomically close attempt + terminal job + receipt; terminal job with a different completion key cannot change outcome |
| RecoverExpired | Bounded batch → expired/requeued/exhausted counts | Lock each candidate and recheck expiry/current generation with DB time; close attempt and queue/fail job together. Restart reruns scan, no in-memory checkpoint required |

Lock order, scoped by operation: submit arbitrates its submit key before creating a new job; claim arbitrates its acquisition key before selecting/locking a job, then current attempt; renew/complete/recover lock existing job then attempt and their own operation receipt. Read-only receipt replay lookup is not authority to mutate. Data must supply one cycle-free cross-operation lock graph, unique-conflict handling and bounded deadlock retries before code; a universal job-first rule cannot precede acquisition-key arbitration. Duplicate HTTP claims cannot take different jobs under the same acquisition key.

Immediate eligibility requires QUEUED, an authorized exact task/schema capability and a free caller slot. No due-time, priority or FIFO promise exists in M2. Claim increments generation and total ownership-attempt count exactly once at commit, including claims whose delivery is lost; transport replay and renewal do not consume attempts. Recovery requeues only if the committed count is below the configured maximum. DB time sampled after lock acquisition determines validity: valid when now < deadline; expired when now >= deadline. Renewal stores max(previous deadline, now + configured lease duration). Limit transaction duration; never use a transaction-start timestamp that is stale after lock wait. A valid mutation can commit later while conflicting recovery waits; this is the proposed serialization point, not a wall-clock promise about handler execution. The selected DB clock is an operational assumption: detect significant clock jumps, alert and stop granting new claims until reconciled; it is not a globally monotonic-clock guarantee.

Recommend PostgreSQL conditional updates/row locks with READ COMMITTED plus unique constraints; actual SQL and constraint design are Data work after acceptance. SKIP LOCKED is a queue contention option and supplies neither strict FIFO nor a generally consistent query view ([PostgreSQL locking clause](https://www.postgresql.org/docs/18/sql-select.html#SQL-FOR-UPDATE-SHARE)). A READ COMMITTED statement may observe committed changes; check predicates under locking/conditional writes rather than assuming a prior SELECT remains valid ([transaction isolation](https://www.postgresql.org/docs/18/transaction-iso.html)). Other isolation strategies are possible but need retry contracts and real DB evidence.

Logical access patterns before index/DDL choice:

| Access | Proposed index candidate / justification / cost |
|---|---|
| Submit dedup | Unique scope + key; identity arbitration for selective equality query; extra insert/storage cost |
| Eligible immediate task | Task/schema + accepted time + job ID over QUEUED subset; filter/order limited claim; selectivity depends on queue and task mix, write cost on queue entry/exit |
| Recovery | Lease deadline + job/attempt identity over ACTIVE subset; range scan bounded expired work; heartbeat updates cause index write amplification |
| Query/replay | Job/attempt identity and scoped request key uniqueness; exact lookup; receipt retention adds storage and cleanup cost |

These are candidates, not asserted query plans. Data must provide exact queries, columns, constraints, selectivity estimates and write costs; Reliability measures actual plans/workload. Migrations must support rolling compatibility and backfill, with released migrations immutable.

Query reads the authoritative committed store in M2, including a coherent public outcome snapshot; do not route to a lagging replica or report a store timeout as 404. After acknowledged creation, an authorized query finds the job until the advertised retention ends, subject to service availability. Read-after-complete sees that terminal outcome or later identical replay; durable data loss/failover limits remain selected-DB configuration assumptions. Mutation response uncertainty is reconciled by the same operation key; a query without a received jobId cannot locate a lost submit by magic. M2 provides same-key SubmitOrReplay, not a new key-lookup endpoint.

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

Command equality proposal: UTF-8 JSON and a versioned JCS/RFC 8785 canonical command containing task/schema and every behavior-changing field. JCS rejects duplicate properties, uses binary64 number serialization and deterministic property ordering; applications needing exact large integers/decimal values encode them as schema-defined strings ([RFC 8785](https://www.rfc-editor.org/rfc/rfc8785#section-3.1)). This replaces the earlier unspecified promise to preserve arbitrary numeric precision. Core defines normalization version/fixtures; Data stores canonical bytes + digest/version and compares canonical content, rather than relying solely on hash equality. SDK does not invent the server fingerprint. Whitespace/object-key-order differences replay; arrays, distinct string code points and different task/schema values remain distinct. No Unicode normalization is implied. Existing keys keep their original canonicalization version through upgrades. Worker completion equality uses the same canonical profile; alternative is strict byte equality with documented formatting conflicts, not a custom unreviewed algorithm.

Envelope policy proposed: reject missing/null required mutation fields and unknown behavior-changing request fields; task payload is a non-null JSON object validated against its immutable registered schema, whose unknown-field policy is explicit. Numeric values have documented binary64 semantics; integer-valued JSON numbers in this Chronos profile are restricted to the interoperable safe-integer range, while exact large integers/precision-sensitive decimals require schema-defined strings. Validate restrictions from the raw numeric token before lossy conversion. Success result may be any schema-permitted JSON value including null; missing result and FAILED are distinct. Server times are UTC RFC 3339 strings ([RFC 3339](https://www.rfc-editor.org/rfc/rfc3339)); job/attempt IDs are opaque strings, not JSON numbers. Bounds apply to raw bytes before parse and canonical payload/result bytes after normalization; nesting/whole-envelope limits still require D7 configuration, so payload limits alone do not claim parser resource safety. No arbitrary class names/polymorphic deserialization. JCS implementation/library and cross-language numeric/Unicode conformance remain UNVERIFIED. Retained keys require their original canonicalizer to remain available and incoming replays to normalize under that stored profile; a new parser/library version may not silently alter equality.

Compatibility: v1 clients tolerate additional optional response fields, preserve safe unknown error codes and never interpret an unknown lifecycle state as success. Unknown required capability/schema/major protocol rejects before handler invocation; changing required fields, states or canonical equality needs versioned rollout/fixtures. Payload schema versions are immutable: server/operator cannot remove an in-use task/schema or overwrite its validator while active jobs exist in this M2 proposal. Existing scoped receipt replay does not revalidate against a newly edited catalog. Local registration duplicates fail startup; remote catalog membership does not require a live worker. Java signatures remain a later public API proposal using these semantics; no supported JDK/Spring version matrix is proven yet.

Proposed initial configurable bounds for Owner decision: payload/result 64 KiB each, safe error 4 KiB, key 128 UTF-8 bytes, one assignment per claim, lease 30 seconds, renewal every 10 seconds, at most 3 total ownership attempts per job, HTTP request deadline 5 seconds. These are starter policy values, not performance measurements. Lease timings must be validated against pauses/network/storage latency. Worker validates encoded size/schema before invoking handler; serialization/result error becomes structured failure if ownership still valid. If renewal fails or lease validity is uncertain, stop taking work, attempt cooperative handler interruption and do not assume external side effects stopped.

Receipt/key retention proposed: job/outcome and linked submit/acquisition/completion receipts retained 7 days after terminal outcome; never delete active-job receipts. Empty acquisition receipts are retained 7 days from creation and advertise their own dedupUntil. Expose dedupUntil on terminal query/receipt. Clients may replay during the advertised window; after purge, job may be 404 and a reused submit key may create a new job. Retired/empty acquisition replay must not grant new work; expired receipt keys return an expiry error if a tombstone is retained, otherwise reuse outside the window has no dedup guarantee. Renewal receipts can be compacted after retirement only when all further renewals return stale and cannot change state. Cleanup cannot remove idempotency evidence from active jobs. Retention values/GC/tombstone policy require Owner/Data agreement before implementation.

## Worker lifecycle and side effects

Authenticate, create fresh session, build local codec/handler map, claim only with a free executor slot, verify assignment compatibility, invoke handler outside transaction, renew on an independent bounded timer, encode outcome, complete with stable request key and retry the receipt if response was lost. Process assignment at most once per attempt in that live session; replayed claim does not schedule a second local invocation. Restart uses a new session and does not resume a prior token blindly. Shutdown stops claiming, keeps renewal while draining within configured grace, then cooperatively interrupts; leftover jobs recover through expiry. Handler exceptions are FAILED without automatic business retry in M2.

SDK lifecycle proposal: explicit synchronous handler registration and submit/query/poll first; annotations, async result futures and starter discovery defer to M6. Validate codecs/config/duplicate registration before network intake. Startup authentication or connection failure reports not-ready/disconnected with a bounded caller-visible deadline; do not claim work before valid configuration and an authenticated successful protocol exchange. Reconnect stops intake while disconnected, preserves in-flight request keys for bounded replay and uses a fresh session on process restart. Application-visible context includes jobId/attemptId and correlation, never application-controlled lease credentials. SDK closes only resources it owns; borrowed clients/executors remain caller-owned. Drain grace, request retry budget/backoff and local execution concurrency are D7 values requiring acceptance. Reaching an SDK wait/drain deadline does not cancel the durable job.

Unknown local handler/unsupported decoder after a committed assignment yields sanitized HANDLER_UNAVAILABLE or PAYLOAD_INCOMPATIBLE failure through guarded completion, without invoking a different handler or silently looping; at expiry it becomes stale and recovery governs. No compatible live worker before claim leaves QUEUED. A handler exception after invocation sets effectsUncertain=true conservatively; known pre-invocation validation failures can record false unless an earlier attempt expired. Preserve this marker on subsequent recovery. Outcome-report failure never authorizes local handler reinvocation under the same attempt. SDK supports bounded jittered retry of safe operation replay, not business retry; store error/deadlock retry belongs to bounded Data policy and must not bypass HTTP receipt identity.

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
| F14 I8 | Duplicate local registration, assignment/schema mismatch, exact-number/Unicode/null/unknown-field compatibility variants | Startup fails for duplicate registration; incompatible assigned payload fails before invocation; JCS vectors and versioned fixtures agree; SDK + wire tests |
| F15 I4/I6/I8 | Startup outage, reconnect, borrowed-resource shutdown and drain while handler/report blocked | No intake while not ready; same-key report reconciliation; borrowed resources preserved; drain stops SDK intake within accepted grace but cannot promise handler termination |
| F16 I2/I8 | Sentinel secrets in handler error/payload, unauthorized renew/complete and unsupported protocol | Denied writes/effects absent; logs/errors/metrics redact secrets/tokens and avoid unbounded labels; server/worker negative fixtures |
| F17 I1/I2 | Accepted submit/complete, query authoritative store; store unavailable; catalog schema removed with active jobs | Query sees committed snapshot until retention; outage is unavailable, not 404; unsafe catalog replacement/removal rejected and existing receipt replays |

Reliability oracle mapping is in ARCH-PROP-001. Its F-xx IDs are distinct from these Fxx IDs. All applicable proposed M2 oracles remain contingent on D1–D8 acceptance; later cases remain explicitly deferred or conditional, never marked PASS/N/A from absent infrastructure.

Deterministic observation contract: first prove receive/commit/lock/expiry/effect boundary, then inject/release faults. Safety assertions compare committed state/receipt plus independent invocation/effect counts. For a single eligible job fixture, available store/server, compatible free worker and an accepted scan/poll/retry budget, bound recoverability by remaining lease + one successful recovery scan + one eligible poll + bounded storage/network work. Large backlog/unavailable worker has no global completion bound or FIFO guarantee. Recovery scan interval/batch, polling jitter, operation lock/request budgets and cleanup/drain deadlines must be chosen and measured before executable acceptance; until then time-based oracles are PARAMETER_PENDING, not pass claims. Tests need real selected DB/process restart and real-time expiry cases alongside virtual-clock policy tests. Performance workload/SLO remain Q13 data gaps.

Timeout/cancel/priority/business retry and broker commit/publish/ACK tests are future contracts, not fabricated M2 outcomes. If Owner chooses broker delivery, this draft must add durable event intent in the same job transaction, publisher retry/dedup, consumer ACK rules and poison-event disposition before code begins. With pull, there is no publish/ACK window; claim/completion response loss above is the equivalent window to verify. Broker delivery never inherits an exactly-once claim from this design.

## TRADE-OFF / acceptance

Pull avoids a DB/broker dual-write dependency but adds polling latency/load and no strict fairness. Bounded lease recovery prevents permanently lost ownership but permits repeat/overlap and requires idempotent handlers; one attempt with UNKNOWN/manual intervention is the simpler alternative if Owner accepts lost automatic progress. Separate job/attempt makes these limits visible without inventing a full workflow entity model.

Design acceptance needs explicit Owner approval of ARCH-PROP-001 decisions and Core/Data/SDK/Reliability feedback reconciliation. Numeric limits, public operation/error model, retention/canonicalization, persistence/clock semantics and downstream uncertainty are all review points. Coordinator then assigns exact implementation/test paths; Release builds foundation; Reviewer approves exact tested code revisions. No test command, performance number or integration approval exists yet.
