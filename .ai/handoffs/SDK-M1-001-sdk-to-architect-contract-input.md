# SDK-M1-001 - Client and worker contract input

author: sdk
task_id: M1-SDK-INPUT-001
status: PROPOSED_INPUT_READY
updated_at: 2026-10-03 20:04 +07:00 (Asia/Saigon)
source_branch: agent/sdk/m1-input
recipient: Architect (M1-DESIGN-001); Coordinator; Reliability (test planning)
references: GOV-001; GOV-002; COORD-001; COORD-ROAD-001
baseline_sha: 9a45b111847685255cf1006a2b839d31ecdf31a4
assignment_sha: 4a18310a0af1f982ab326b77c496b4721fd8ef18
planning_sha: 2ce8a98f810cd00d268332ad68df45fc8f99a866
implementation_sha: none

## ACK and scope

ACK COORD-ROAD-001 at assignment_sha, specifically the SDK assignment in .ai/handoffs/COORD-ROAD-001-coordinator-to-agents.md and .ai/context/TASK_BOARD.md.
Read ROADMAP.md and CODE_NOTES.md at planning_sha; baseline AGENTS/rules/prompt/workflows at baseline_sha; operational context at assignment_sha using git show.
Only authored this handoff and .ai/status/sdk.md. No SDK code, server/schema edits, notebook edits, public signatures, selected dependencies or accepted API.
All requirements/options below are SDK input for Architect + Owner, not guarantees of current Chronos. No accepted applicable design/ADR was present in the referenced sources.

## WHY / WHAT / FLOW / FAILURE / TRADE-OFF

WHY: the first external application must submit a job, run a registered handler and understand the eventual outcome without depending on server internals or mistaking a lost response for a rejected job.
WHAT: agree semantic contracts for submission, task registration, execution context, outcome query, error mapping, payload encoding and resource lifecycle before choosing Java signatures.
FLOW proposed: validate local configuration -> register handler before accepting work -> serialize and submit -> server acknowledges according to accepted durable boundary -> worker decodes/invokes -> reports outcome -> client queries outcome. Server owns authoritative state transitions.
FAILURE: response loss can leave submit acceptance unknown; handler side effects can complete before outcome reporting fails; shutdown/cancellation can leave execution in flight. These require explicit contracts, not silent resubmission.
TRADE-OFF: explicit setup and bounded polling make the first demo easier to explain; fluent builders, annotations, async APIs and starter discovery improve convenience but add lifecycle/compatibility surface. No style is selected here.

## Minimum M2 demo and later boundaries

Proposed minimal demo: one named handler with an explicit payload contract; one application submits a valid payload, observes a durable job identity when acknowledged, and queries eventual success or handler failure using a bounded deadline. Setup, stop and negative cases must be reproducible.
Transport, persistence, authentication assumptions and runnable commands must come from accepted M1/M0 decisions. A one-worker demo is not concurrency or exactly-once evidence.
M2 needs explicit handler registration, client/worker start/stop, payload validation, submit/query/outcome semantics, user-visible errors and correlation identifiers. Decide duplicate-submit policy now even if advanced idempotency is later.
M3-M5: lease/heartbeat recovery, retry/timeout/cancel and messaging advanced behavior follow accepted contracts; preserve extension needs without advertising unsupported methods or guarantees.
M6: starter discovery/configuration, reconnect behavior, external-app quickstart and version compatibility. Do not require a complete starter for M2 unless Owner changes scope.
Workflow/DAG, production tuning and package publishing are outside this preparation assignment.

## Contract questions and proposed requirements

| Surface | Proposed externally meaningful contract | Architect decisions needed |
|---|---|---|
| Submit | Task identity, payload, correlation and optional logical-request identity; acknowledgment with stable job identity | When is acceptance durable? How can the caller reconcile ambiguous acceptance? Duplicate scope/retention and same-key/different-payload behavior? |
| Registration | Explicit mapping of supported task identity and payload decoder to a handler, complete before consumption | Local registration versus server capability announcement? Duplicate registration, unknown task and version mismatch policy? Is submit before registration allowed? |
| Execution | Bounded context with public job/attempt correlation and cooperative stop information if supported | Which identity changes per attempt? Ownership validity/stale completion response? Synchronous/async handler contract and concurrency bounds? |
| Query | Public domain vocabulary, safe failure information and outcome representation; distinguish unavailable from unknown identifier | Accepted lifecycle/transitions, result retention/size, read consistency and visibility after submit acknowledgment? |
| Errors | Distinguish local invalid input/configuration, definitive rejection, transport ambiguity and eventual handler failure | Stable error categories, machine-readable retry hints, authorization/not-found disclosure, safe details and cause mapping? |
| Request retry | Separate transport retry from a new logical submission and from another execution attempt | Which operations are safe to replay? Budgets/backoff/deadlines and retry hints? No automatic mutation replay without approved safety contract |
| Lifecycle | Validate before start; stop new intake, handle in-flight work within a configured drain bound, release owned resources | In-flight policy at drain expiry, report delivery/ACK ordering, reconnect and resource ownership? Start/readiness behavior during server outage? |
| Serialization | Explicit format/version, task payload schema and bounded decoding; no arbitrary server class names on the wire | Selected codec, null/unknown field behavior, malformed/incompatible payloads, time/number encoding and schema evolution rules? |
| Starter | Clear enablement/configuration, explicit override precedence and one owned lifecycle per worker/client | Java/Spring versions, bean discovery versus explicit registration, multiple contexts/workers, disabled mode and custom codec/client lifecycle? |

Public boundary: task and job identities, payload/result contracts, safe errors and documented execution context belong to the proposed public surface. Storage rows, SQL details, broker partitions/offsets, internal scheduler classes and internal state enums do not.
A future ownership token may be carried by SDK protocol code, but should not become an application-controlled capability merely for implementation convenience. Architect determines protocol versus handler-facing fields.
Client should depend only on accepted external contracts. No shared-contract module or server API path is claimed; Architect proposes mapping and Owner accepts it.

## Failure behavior to decide and verify

| Timeline / trigger | Proposed observable behavior and acceptance question | Future verification |
|---|---|---|
| Invalid local payload or configuration | Reject before outbound submit when validation is available; server still independently validates | Assert no outbound call, actionable safe error; server rejection has no accepted job |
| Commit succeeds; submit response is lost | Report acceptance unknown, preserve logical-request identity; reconcile/replay only under selected policy | Inject lost response after commit; same request cannot silently create uncontrolled duplicates |
| Same logical-request key with changed payload | Explicit conflict or an explicitly approved alternative; never silently substitute payload | Concurrent same-key requests with equal and different payload; verify server contract |
| Task absent locally or incompatible decoder | Apply documented failure/disposition; do not loop forever or run another handler | Unknown task, duplicate registration, malformed and unsupported payload version |
| Handler throws | Represent execution failure separately from client transport failure; sanitized error information | Query agrees with accepted state/history; no secret stack/payload leakage |
| Side effect succeeds; outcome report/ACK is lost | Execution may be observed again under selected delivery semantics; no exactly-once claim | Failure after side effect before report/ACK; demonstrate limits and downstream dedup requirement |
| Query returns no job after submit acknowledgment | Distinguish documented visibility delay/retention from missing durable acceptance | Verify read consistency policy; unavailable response is not terminal job failure |
| Worker shutdown during handler | Stop intake, drain/cancel according to explicit bounds; do not report success for abandoned work | Block handler/report, request shutdown, check resources and recorded outcome; completion bounds TBD |
| Worker reconnects; stale ownership reports completion | Server authoritatively rejects or handles according to accepted ownership contract | Stale completion/heartbeat after recovery; SDK exposes actionable outcome without overwriting state |
| Request deadline versus job timeout/cancel | Local waiting timeout does not imply remote cancellation or reversed side effect | Lost connection/deadline while execution continues; later query matches server truth |

Outcome expectations in this matrix are proposals. Exact transitions, retry counts, bounds, identities and stale response codes remain TBD until accepted design. SDK cannot enforce database dedup or prevent arbitrary handler side effects alone.

## API options for Architect + Owner

1. Explicit client and handler registration first, optional fluent facade later versus fluent/annotation-first. Proposed preference for M2: explicit wiring reduces hidden discovery/lifecycle behavior; convenience can be layered later without changing semantic guarantees.
2. Bounded query/poll for the demo versus a future/callback completion API. Proposed preference: bounded query first; async APIs need separate executor, cancellation and completion-thread contracts.
3. Logical-request key plus reconciliation versus no dedup with explicit ambiguous-submit handling. Proposed preference: stable caller-provided request identity only if server implements/accepts matching dedup semantics. If deferred, no silent automatic submit retry; acceptance unknown must be visible.
4. Explicit versioned payload contract versus implicitly serializing arbitrary application objects. Proposed preference: explicit schema/version boundary; codec and Java binding remain undecided.
5. Transport adapter boundary versus exposing broker/storage primitives. Proposed preference: adapter boundary with safe public errors; complexity cost must be justified by accepted transport choice.
These are design inputs, not Java signatures or accepted defaults.

## Decisions required before implementation

Architect drafts and Owner accepts:
- D1: public submit/query/registration/execution/result vocabulary and contracts, including acknowledgment/read consistency and unknown-handler policy.
- D2: ambiguous submit reconciliation, logical-request identity/dedup scope, conflict/expiry policy and permitted transport retry operations. Align Core/Data.
- D3: task/payload/result versioning, codec choice, safe decoding/limits, error categories, authentication/trust boundaries and sensitive-data handling.
- D4: worker execution/lifecycle semantics, attempt/ownership identifiers, reporting, drain/reconnect and forward compatibility for M3-M5.
- D5: Java/build/Spring compatibility targets and exact server API/shared-contract/example/build paths and owners.
Owner/Coordinator additionally resolves developer/develop and Release activation per M0-GOV-001. These do not block this analysis but block normal integration/build work.
Requested handoff: Architect incorporate D1-D5 or explicitly defer with documented M2 limitations; Coordinator assign resulting code paths/tests only after acceptance; Reliability align cross-module scenarios with accepted invariants. No outbound chat tool message is sent by this handoff.

## Test requirements and ownership

SDK unit/contract tests later: local validation and request counts; error mapping including acceptance unknown; logical-request identity preservation; codec round-trip and incompatible/oversized data; handler registration conflicts; deterministic start/stop/drain/resource ownership; retry budget/deadline boundaries after defaults are accepted.
Starter tests later: invalid/disabled configuration, user override precedence, one worker per accepted scope, lifecycle shutdown, supported version combinations and external-app integration.
Reliability cross-module tests: lost submit response after durable acceptance, concurrent duplicate submit, unknown handler policy, side-effect/report crash, stale ownership and restart. Real infrastructure selection awaits M1. SDK unit tests alone cannot establish durable acceptance or side-effect dedup.
Acceptance for M2 demo: reproducible setup; submit/execute/query success and handler failure; invalid payload/unknown task/ambiguous response visible; safe logs and traceable identifiers; explicit limitations.
Code/test paths, names, commands, tested SHA and environment: TODO. Tests: NOT_RUN; no implementation/build exists in inspected baseline. No performance or compatibility result claimed.

## CN hotspots for Owner notebook

| Note | Future hotspot (path/symbol/SHA TODO) | Proposed invariant and failure timeline | Trade-off and evidence to obtain |
|---|---|---|---|
| CN-06 | Submit retry/reconciliation boundary | One logical request identity survives lost response; dedup does not itself dedup handler effects | Retry convenience versus duplicate creation; lost-response and same-key/conflict tests |
| CN-09 | Registration, codec and worker/starter resource lifecycle | Consume only after valid setup; bounded shutdown and safe version mismatch | Explicit setup versus auto-discovery; external integration and drain/version tests |
| CN-11 | Public error/log redaction, correlation, drain deadline | Transport ambiguity is observable without secrets; stop does not falsify remote outcome | Diagnosis versus data exposure; log capture, unknown-outcome and in-flight shutdown tests |

Learning status: TODO. Code/symbol/implementation SHA and regression evidence: TODO/NOT_RUN.
60-second design explanation for future validation: a client may lose the response after a job was accepted. It must distinguish unknown acceptance from rejection and preserve request identity. A worker can also perform an effect before its result is recorded, so submit dedup does not prove exactly-once effects. We need accepted server contracts and failure tests before convenient SDK retry or shutdown behavior can be called safe. This is proposed reasoning, not an implementation walkthrough.

## Risks and next action

Affected roles: Architect defines contracts; Core/Data supply server guarantees; SDK maps them to caller/worker behavior; Reliability verifies distributed windows.
Main risk: a convenient fluent API can conceal acceptance ambiguity, retry amplification or worker shutdown failure. Proposed mitigation is explicit semantic categories and bounded lifecycle with tests; cost is additional caller handling and contract work.
Preparation deliverable is ready for consumption. Next safe action: Architect/Coordinator read this publication by immutable SHA, record disposition and publish accepted design/explicit implementation assignment. SDK remains available for contract clarification; production implementation remains blocked.
