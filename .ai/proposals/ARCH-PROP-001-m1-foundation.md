# ARCH-PROP-001 — missing ownership and M1 foundation decisions

author: architect
task_id: M0-ARCH-001; M1-DESIGN-001
status: PROPOSED_NOT_ACCEPTED
updated_at: 2026-10-03 21:13 +07:00 (Asia/Saigon)
source_branch: agent/architecture/m1-foundation
references: GOV-001; GOV-002; GOV-003 at efca8bc9c1758848bce9c7721f19adfdca651277; COORD-ROAD-001 at 4a18310a0af1f982ab326b77c496b4721fd8ef18

## Problem and existing authority

GOV-001 assigns engine/storage/SDK paths but leaves server interfaces, contracts, composition, root build/CI and operations/docs unmapped. Architecture/build/public API are unaccepted. A concrete first-job slice needs these boundaries before production assignments. Recommend the [foundation](../../docs/architecture/m1-foundation.md) and [domain/contract draft](../../docs/design/m1-first-job.md). This proposal grants no writes to the proposed paths and does not edit collaboration/git rules.

GOV-003 settles naming: normal integration is develop; developer is bootstrap only. Release activation and actual develop setup are still operational dependencies. Keep the assigned worktree rooted at bootstrap SHA 9a45b111847685255cf1006a2b839d31ecdf31a4; never treat naming approval as candidate acceptance or branch-creation authority for Architect.

## D1 — exact missing mapping for Owner acceptance

Existing GOV-001 ownership stays in place. New grants below are proposed only; at each path there is one writer. The POM-file exceptions explicitly override module-directory ownership for build configuration if Owner accepts them. Production source/testing ownership in those directories stays with its existing role. Proposed grants do not authorize permanent unrelated path expansion.

| Exact proposed path or subtree | Proposed writer | Decision / boundary |
|---|---|---|
| `chronos-server/chronos-api/` except its `pom.xml` | Core | HTTP/auth/validation adapters only; architecture/API changes still Architect + Owner |
| `chronos-server/chronos-app/` except its `pom.xml` | Core | Server composition/lifecycle/config validation; no storage/messaging implementation |
| `chronos-contracts/v1/openapi.yaml` | Architect | Reviewed wire specification; public-contract approval still Owner |
| `chronos-contracts/v1/fixtures/` | Architect | Normative request/response/error/schema fixtures; Reliability writes executable verification in its existing paths |
| `pom.xml` | Release | Root reactor/build configuration only |
| `chronos-server/pom.xml`, `chronos-sdk/pom.xml` | Release | Aggregator build configuration only |
| `chronos-server/chronos-engine/pom.xml`, `chronos-server/chronos-scheduler/pom.xml` | Release | Core module build-file exceptions |
| `chronos-server/chronos-storage/pom.xml`, `chronos-server/chronos-messaging/pom.xml` | Release | Data module build-file exceptions; messaging only when accepted |
| `chronos-server/chronos-api/pom.xml`, `chronos-server/chronos-app/pom.xml` | Release | Proposed API/app build-file exceptions |
| `chronos-sdk/chronos-java-sdk/pom.xml`, `chronos-sdk/chronos-worker-sdk/pom.xml` | Release | SDK module build-file exceptions |
| `chronos-sdk/chronos-spring-boot-starter/pom.xml`, `chronos-sdk/chronos-test/pom.xml` | Release | SDK module build-file exceptions; starter deferred |
| `integration-tests/pom.xml` | Release | Reliability build-file exception if separate integration-test module selected |
| `.mvn/wrapper/`, `mvnw`, `mvnw.cmd` | Release | Pinned wrapper/runtime-download configuration, reviewed source/checksums |
| `.github/workflows/build.yml` | Release | Build/test CI only, no publish/deploy workflow grant |
| `.gitignore`, `.editorconfig` | Release | Explicit root hygiene/build configuration |
| `compose.yaml`, `.env.example`, `ops/local/` | Release | Local reproducible infrastructure/config examples; no secrets/deploy authority |
| `docs/operations/` | Release | Local setup/outage/migration/runbook docs within explicit tasks |
| `docs/sdk/`, `examples/java-client/`, `examples/spring-worker/` | SDK | Public guide and runnable consumer examples, no notebook edit |

Architecture/design/ADR/navigation remain Architect-owned. README and operational context remain Coordinator-owned. ROADMAP.md and CODE_NOTES.md do not gain a new owner/grant here; CODE_NOTES remains Owner's notebook. Unlisted paths (including server Dockerfiles, release-publishing workflows, deployment and cloud config) stay unassigned; if selected later, propose exact mapping before writing. A spec-only contracts directory has no POM/runtime module by default.

Alternative: place API/composition inside engine to avoid new modules, but then framework/HTTP dependencies enter the domain boundary. Alternative build ownership: each module role edits its POM; less Release bottleneck but increases dependency drift. Recommend the explicit Release POM exceptions plus owner handoffs for semantic dependency changes. Core/Data/SDK must review dependency changes affecting behavior; Release cannot use build ownership to change business semantics.

## D2–D8 — concrete architecture/policy decisions requested

| ID | Recommended proposal | Alternative / principal risk | Required Owner decision |
|---|---|---|---|
| D2 | Modular server + HTTP worker pull + PostgreSQL authoritative queue | Broker-first moves event-intent/publish/ACK/replay into M2; pull adds polling load/latency | Accept boundaries and DB/delivery family or select alternative |
| D3 | Java 21 API target, Maven wrapper/reactor, supported Spring Boot family pinned in Release M0 manifest | Java 25 target / Gradle / specific older-client compatibility; exact release matrix unresolved | Accept target/build direction and desired SDK client baseline; accept exact versions after manifest verification |
| D4 | Job + Attempt, conditional claim/renew/complete, at-least-once handler recovery; handler failure terminal in M2 | No automatic recovery/manual UNKNOWN disposition reduces complexity but loses automatic progress | Accept states/invariants and explicit external-effect uncertainty |
| D5 | Minimal expiry/renew/fencing/bounded recovery in M2; advanced M3 and M4 features remain later | Keep M2 strictly single-attempt demo, explicitly leave crashes unresolved | Accept scope/dependency shift; Coordinator replan, not Architect self-assignment |
| D6 | Proposed v1 submit/query/claim/renew/complete HTTP operations, error taxonomy, explicit JSON codecs/canonicalization and scoped key replay | Opaque bytes avoid canonicalization but make semantically equivalent payloads conflict; generated public SDK coupling adds cost | Accept operation/error/data contract before Java SDK signatures are designed/implemented |
| D7 | Configurable 64 KiB payload/result, 4 KiB error, 128-byte key; lease 30s/renew 10s, 3 total attempts, request 5s; terminal receipt retention 7d | Tuned limits/retention need workload and downtime expectations; proposed numbers not benchmark evidence | Confirm/change all policy values and retention/GC contract; authorize target workload/budget separately |
| D8 | Single authenticated owner scope demo, distinct client/worker capabilities, operator task allowlist, TLS beyond loopback, secret/payload redaction | Multi-tenant requires full isolation design; unauthenticated exposed demo unacceptable under proposed threat model | Accept demo exposure/auth credential distribution and retention of sensitive outcomes; no deployment implied |

## Compatibility, migration and trade-offs

There is no application/schema/public SDK to migrate yet. Introducing exact modules/wire v1 still needs Owner acceptance and build/dependency validation. Future storage migrations must preserve active keys/attempts and canonicalization version; cleanup may not invalidate an advertised dedup window. Wire additions need compatibility fixtures and older-worker behavior; breaking change requires a new contract version and rollout plan. Broker/outbox adoption later must preserve job identities, scope, attempt fencing and receipt semantics; event IDs/dedup/ordering/retention require a separate reviewed contract.

Affected roles: Core implements use cases/API/composition after accepted ownership; Data supplies atomic ports and schema; SDK implements wire/public-client lifecycle; Reliability exercises deterministic failures; Reviewer evaluates exact evidence; Release prepares build/integration. Benefit: a small, explainable end-to-end contract with failure behavior. Risks: lease recovery repeats effects, polling adds DB work, canonicalization/receipt retention require careful compatibility, centralized POM ownership can serialize dependency work.

## Decision / evidence gate

Owner can approve D1–D8 independently or request changes. Acceptance must name the immutable draft revision and concrete approved choices; Coordinator records evidence and Prompt Master updates ownership rules only after approval. Architect then records accepted ADR(s); do not pre-create ACCEPTED ADRs. Release role is now verified/assigned for planning; actual develop setup, exact version manifest, runnable build/test/CI evidence and ownership remain M0 prerequisites. Core/Data/SDK/Reliability inputs have been reconciled below, but none of those authors has approved this revised draft. Current deliverable is a decision proposal, not READY to implement, Reviewer APPROVED or integrated DONE.

## PM-ARCH-001 synthesis — immutable source manifest

ACK PM-ARCH-001 at fd56daf45288a4d4c84d6c81da553bc0bee997ec:.ai/handoffs/PM-ARCH-001-prompt-master-to-architect-input-reconciliation.md. Direct Owner reconciliation instruction independently verified in Prompt Master user turn 01a10207-9021-7f32-bb46-9ebc428d4ffd. This is the sole per-input synthesis record; the design defines behavior and status/handoff point here rather than replicate this matrix.

Aliases below always refer to these immutable SHA/path pairs, including adjacent author status. Consumers use git show SHA:path, not merge to read. Full substantive files and author status were read. Historical publication-blocked/naming/Release-absent statements in those frozen files are preserved; the PM receipt and later Coordinator decisions resolve them.

| Alias | Full source SHA | Substantive paths; author status read |
|---|---|---|
| Core | da760dae97fbdc9d92c925f949e35eb3da95d6d8 | `.ai/handoffs/CORE-M1-INPUT-001-core-to-architect.md`; `.ai/status/core.md` |
| Data | e3d0027099fbdd7b82c9a9253d668018f3256bad | `.ai/handoffs/M1-DATA-INPUT-001-data-to-architect.md`; `.ai/status/data.md` |
| SDK | 12893a765a56526ea5630a83f8c24e54a7fc2122 | `.ai/handoffs/SDK-M1-001-sdk-to-architect-contract-input.md`; `.ai/status/sdk.md` |
| Reliability | edfe35d1ad464b967e12758298dcc786d4996f6a | `test-support/M1-failure-matrix.md`; `.ai/handoffs/REL-M1-001-reliability-to-coordinator-architect.md`; `.ai/status/reliability.md` |
| Reviewer | 6b693a820a8693b6d5dd414e6ede2a535c2e58ab | `.ai/reviews/M0-REVIEW-001-baseline.md`; `.ai/handoffs/M0-REVIEW-001-reviewer-to-coordinator.md`; `.ai/status/reviewer.md` |
| Release | 18192bf2073fdef57a738b1013cc99de7bc2e6c6 | `.ai/releases/2026-10-03-M0-foundation-plan.md`; `.ai/status/release.md` |
| Coordinator | 7834520200abb24738feb55a5923518c23c4fc51 | `.ai/context/CURRENT_STATE.md`, `TASK_BOARD.md`, `DECISIONS.md`; `.ai/status/coordinator.md` |
| Architect prior | c33a045ba7ae2583a608698dc2b8b714ae2697fd | `docs/architecture/m1-foundation.md`; `docs/design/m1-first-job.md`; this proposal; author status/handoff |
| Planning | 2ce8a98f810cd00d268332ad68df45fc8f99a866 | `ROADMAP.md`; `CODE_NOTES.md` (read-only Owner notebook) |
| PM receipt | f4eb0e3d1a97cf8f42900d6dc06ef3249897d0a8 | `.ai/handoffs/PM-PUB-001-prompt-master-to-coordinator-publication-receipt.md`: all eight refs verified; integration refs unchanged |

Disposition vocabulary: **covered by prior draft** means a proposed answer already existed, not author/Owner acceptance; **proposed resolution** means this revision supplies or changes an answer; **Owner choice** means viable scope/contract alternatives; **needs data** means the premise/numeric target remains insufficient. Sources ask questions/options and do not endorse our technology/public API recommendation. No dissent or consensus vote is invented.

## Agreement, gaps and decision readiness

Shared constraints: acknowledgment only after a declared durable boundary; atomic eligibility/ownership writes; no external calls within DB transactions; transport unknown distinct from handler failure; stale state protection distinct from external-effect idempotency; explicit SDK codecs/lifecycle; real DB and independent effect observations for failure evidence. Our draft implements these constraints as proposals.

No published input states a contradictory accepted contract. Remaining alternatives are genuine Owner choices: narrow single-executor/no-recovery demo versus bounded distributed recovery, pull versus broker/intent, JSON equality/compatibility profile, build/client baseline and scope of network trust. Concrete draft defects/gaps discovered in synthesis: universal job-first lock wording conflicted with acquisition-key-first arbitration; late schema/catalog edits could defeat receipt replay; arbitrary-number equality was unspecified; registration mismatch/readiness/drain/resource ownership and query oracle needed more detail. Revised design supplies explicit proposals for each. Numeric retention/timing cannot be justified from unspecified workload; values stay examples, not approved defaults.

| Decision | Contributing input / agreement or choice | Recommendation / alternative / benefit-risk | Affected boundary, compatibility and validation | Readiness |
|---|---|---|---|---|
| D1 mapping | Release mapping request; SDK public/internal boundary; Reviewer CN-01; Coordinator missing paths | Keep exact mapping above and Release POM exceptions; module-owned POM alternative reduces bottleneck but raises drift | Core API/app, Architect wire specs, SDK examples, Release build/ops; dependency/clean consumer build checks; active rules change only after Owner acceptance | Ready for direction choice; Owner must explicitly accept each grant/exception |
| D2 architecture | Core C02/C03/C09; Data options/D05/D08–16; Reliability Q05/Q08/Q09 | PostgreSQL-backed HTTP pull; broker-first adds durability/ACK/consumer work before M2. Fewer boundaries vs polling/DB pressure | Server/worker protocol and Data atomic ports; no strict FIFO; test actual DB competing claim and lost response, not mocks | Ready for direction choice; versions/workload pending |
| D3 runtime/build | SDK D5/Starter; Release build/CI/artifact request; Reviewer CN-01 | Java 21 target/Maven/compatible Boot family; Java25/Gradle alternative depends on consumer requirement | SDK independent compile target; server executable JAR, SDK library JAR; pin manifest and verify dependency graph, real DB env, clean build/client matrix | Direction ready; exact versions and client support window NEEDS_DATA/Release follow-up |
| D4 domain/delivery | Core C01/C04–08; Data D02–07/D13; Reliability Q02–07 | Job+Attempt, guarded receipts and bounded ownership recovery; single-attempt UNKNOWN/manual handling alternative | At-least-once redelivery opportunity within budget, no guarantee of eventual success or once-only effects; terminal handler failures, generation checks; F01–F08/F12/F17 | Ready for semantic choice; draft recheck needed before accepted contract |
| D5 milestone | Core trade-off/T02/T05; SDK minimal demo; Reliability conditional M3 cases; roadmap M2/M3 | Move minimum expiry/renew/fencing/recovery to M2; narrow demo alternative explicitly leaves recovery unsupported | Core/Data/SDK/Reliability effort grows; Coordinator replans only after approval; no messaging work imported under pull | Ready for scope choice; dates/effort not estimated |
| D6 public contract | SDK surfaces/D1–4; Data dedup/migration; Core C06; Reliability Q01–04/Q10 | Explicit client/registry, authoritative query, replay receipts, JCS restricted numbers/schema versioning; byte equality alternate costs formatting conflicts | Changes proposed wire/equality profile, not existing public API; precision-sensitive fields strings; codec/JCS vectors, mismatch/read-after-write and replay tests F13/F14/F17 | Ready for semantic choice; Java signatures, library pins and compatibility fixtures still follow-up |
| D7 budgets/retention | Data retention/index/write-cost; SDK retry/drain; Core C09; Reliability Q06/Q10/Q13 | Configurable bounded admission/attempts/retry/drain/parse/GC; keep starter numbers as examples until requirements | Poll/scan/batch/lock/drain/retry/nesting/envelope budgets missing; 7d active-key history cost and replay horizon need capacity/outage data; F12–F15 plus selected workload measurement | NEEDS_DATA: payload/arrival/concurrency, outage/replay horizon, pause/drain budget; do not approve numeric examples wholesale |
| D8 security/operations | SDK errors/lifecycle; Reviewer CN-11; Reliability Q12/F-24 | Single owner scope, distinct operator-provisioned client/worker credentials, per-task capability check, loopback demo initially; TLS before external exposure | API auth/authz, trusted codec allowlist, secret/token redaction; mTLS/OIDC/multi-tenant alternative separate design; F09/F16 | Direction choice ready; actual exposure/credential mechanism/lifetime/data retention NEEDS_OWNER_CONTEXT |

## Core input disposition — C01–C09 and T01–T10

| Source IDs | Disposition / answer in revised design | Decision / evidence |
|---|---|---|
| Core C01, T09 | Covered by prior state table; proposed resolution adds mismatch/catalog rules. Core policy, Data atomic write, SDK reporting; RUNNING means committed ownership, not observed handler start | D4/D6; F09/F10/F14/F17 |
| Core C02, T01 | Prior atomic claim; clarify immediate eligibility, exact task/schema/capability, loser other-job/empty and no dispatch from discovery | D2/D4; F03, real DB |
| Core C03, T02/T03 | Owner choice pull vs push; recommended worker pull after claim commit, same-key receipt replay, no local duplicate invocation; expired grant recovered | D2/D5; F04/F08; push requires intent/ACK contract |
| Core C04 | Proposed resolution: job identity vs per-attempt generation/session, checks at claim/renew/complete/recover; now < deadline valid, equality expired, DB clock assumptions/seams | D4/D7; F05/F06 plus real-time expiry |
| Core C05, T05/T06 | Prior fenced writes; new lock order and post-lock time rule, exact expiry and uncertainty; no no-overlap promise | D4/D5/D7; F05/F06/F08 |
| Core C06, T04 | Prior completion receipt replay/conflict and authoritative query; new key cannot rewrite terminal state; current auth required even for replay | D4/D6; F07/F17 |
| Core C07, T07 | Owner choice terminal business failure in M2; count committed ownership grants only; recovery budget separate from business-error retry; M4 retry/backoff/history design deferred | D4/D5/D7; F10/F12, future Reliability F-14 |
| Core C08, T08 | Explicit M4 deferral: no cancel/timeout API or precedence accepted; stop best effort is SDK lifecycle, not cancellation | D5; Reliability F-15/F-16 deferred, separate contract before code |
| Core C09, T10 | M2 immediate only, free slot/capacity=1, bounded poll/recovery; strict fairness/priority/due schedule deferred; exact scan/batch/lock budgets needs data | D5/D7; F03/F15; delay equality test awaits M4 |

## Data input disposition — capabilities and D01–D16

| Source IDs / capability | Disposition / answer | Decision / evidence |
|---|---|---|
| Data accept/repeat, D01–D04 | Prior atomic job+key receipt; normalize/equality scoped command; proposed receipt-first replay/catalog handling and unknown acceptance by same-key retry | D4/D6; F01/F02/F09/F13/F17 |
| Data acquire, D05 | Prior conditional claim; revised acquisition-key-before-job arbitration, count/generation and documented lock graph; no exact SQL/schema invented | D2/D4; F03/F04; SQL/unique/deadlock plans after approval |
| Data renew/finish/recover, D06/D07 | Durable expired scan with conditional predicates, no in-memory checkpoint; DB-time comparison/current session/token; side effects separate | D4/D5/D7; F05/F06/F08/F12 |
| Data query | Proposed authoritative-store snapshot/read-after-ack; unavailable distinct from 404; no history pagination/list API promised in M2 | D6; F17; later query/index workload needed |
| Data event intent/publisher, D08–D10 | Conditional Owner choice: under pull no mandatory event and no publish boundary in M2. Broker choice requires atomic intent, publisher restart/stable identity before implementation | D2/D5; Reliability F-17/F-18/F-21 conditional |
| Data consumer progress, D11/D12/D14 | Conditional broker contract deferred under pull; internal effect+dedup transaction and ACK-after-durable-boundary must be specified if chosen; no early ACK guarantee invented | D2; Reliability F-19/F-21 conditional |
| Data external effect, D13 | Prior no exactly-once boundary; strengthen uncertainty on post-invocation failure and preserve expired history; job-level downstream key vs attempt-level token | D4/D8; F05/F08/F10, independent sink |
| Data reorder/poison/replay, D15 | Deferred M5 ordering/retention/quarantine/redrive under pull; no DLQ module implemented. Broker-first reopens these before code | D2/D5; Reliability F-20 conditional/future |
| Data outage, D16 | Prior unknown commit/response; SDK bounded same-key reconciliation, no false rejection or handler restart from HTTP timeout | D4/D6/D7; F02/F07/F11/F15 |
| Data indexes/retention/migration | Proposed logical access purposes only, validate actual query/cardinality/write costs later; active evidence retained, immutable schema/normalization, expand-backfill-contract and forward-fix planning | D3/D6/D7; F13/F17 plus Release real DB migration predecessor/mixed-version tests; retention horizon needs data |

## SDK input disposition — every surface and SDK D1–D5

| Source surface / local decision IDs | Disposition / answer | Owner decision / evidence |
|---|---|---|
| SDK Submit, Request retry; SDK D1/D2 | Prior durable receipt/key/equality; proposed explicit local rejection vs remote rejection vs unknown acceptance vs eventual handler failure; retry same operation identity within bound, never new logical submission silently | D4/D6/D7; F01/F02/F07/F11 |
| SDK Registration; SDK D1/D3 | Proposed explicit local registration, duplicates startup error; independent immutable server catalog, compatible capabilities before claim; mismatch after claim fails guarded before invocation | D6; F10/F14/F17 |
| SDK Execution; SDK D1/D4 | Prior attempt and bounded worker; public job/attempt correlation separated from private protocol authority; synchronous handler and cooperative stop, no future/async cancellation contract | D4/D6; F05/F08/F15 |
| SDK Query; SDK D1 | Authoritative read, safe error/result and effects uncertainty; missing/expired vs unavailable distinct; bounded waiting is local only | D6/D7; F17 |
| SDK Errors; SDK D2/D3 | Prior stable HTTP/code/retryable mapping plus unknown status/code compatibility; sanitized post-invocation failure uncertainty; auth not-found visibility defined | D6/D8; F09/F10/F16 |
| SDK Lifecycle; SDK D4 | Proposed readiness/disconnected behavior, restart session, same-key in-flight replay, bounded drain and borrowed-resource ownership; no stop-of-external-effect claim | D6/D7; F15 |
| SDK Serialization; SDK D3 | Proposed JCS numeric/string constraints, immutable schema versions, null/unknown-field/envelope policy, RFC3339 output, tolerant additive responses; avoid arbitrary application class loading | D6/D7; F13/F14; library/conformance matrix pending |
| SDK Starter; SDK D5 | Explicit registration/poll M2, discovery/annotations/override/disabled/multiple-context starter tests M6; no unimplemented convenience API advertised | D3/D5/D6; Reliability F-22 partial M2, remaining M6 |
| SDK boundary/examples/version support; SDK D5 | Prior exact path mapping and no runtime shared-domain jar; public consumer fixtures required; Java21/Maven direction proposed, supported Boot/client matrix not inferred from requirements page | D1/D3; dependency graph/clean external consumer tests NOT_RUN |

## Reliability question and scenario disposition

All entries below are PROPOSED oracles, NOT_RUN; accepted decisions must replace PARAMETER_PENDING before executable task assignment. Original Reliability matrix is never edited by Architect. Source Reliability Q-xx/P-xx and F-xx retain their meanings, separate from design I1–I8/F01–F17.

| Source questions | Disposition / target |
|---|---|
| Q-01/P-01 | D6/D8; validation/catalog/mismatch rules → I2/I8, F09/F14/F17 |
| Q-02/P-02 | D4/D6; commit receipt/authoritative query/read consistency → I1/I2/I6, F02/F07/F11/F17 |
| Q-03/P-03 | D4; handler failure terminal, unknown transport separate, uncertainty retained → I2/I7, F10/F12 |
| Q-04/P-04 | D6/D7; scoped canonical equality/key conflict/retention vs external effect → I1/I5/I7, F01/F13; horizon needs data |
| Q-05/P-05 | D4; conditional generation/session/token/clock/arbitration → I3/I4, F03–F07 |
| Q-06/P-06 | D5/D7; recovery actor/budget and bounded fixture observation contract → I4/I7, F05/F08/F12; scan/time parameters pending |
| Q-07/P-07 | M4 deferred; no timeout/cancel/business-retry oracles accepted; lifecycle stop is distinct |
| Q-08/P-08 | D2 choice; broker/intent ordering/versioning deferred under recommended pull, conditional prerequisite if broker selected |
| Q-09/P-09 | D2 choice; ACK/DLQ/redrive deferred under pull; external-effect contract already M2 I7/F08 |
| Q-10/P-10 | D3/D6/D7; explicit codec/readiness/drain/resource compatibility → F14/F15, future starter matrix |
| Q-11/P-11 | M7 DAG/fan-in/partial failure deferred; no new entity/guarantee |
| Q-12/P-12 | D8 minimum M2 auth/denied writes/correlation/redaction/cardinality → F09/F16; deployment context pending |
| Q-13/P-13 | NEEDS_DATA; Owner workload/hardware/latency/SLO/run budget before M9; no invented throughput |

| Reliability scenario(s) | Proposed oracle and milestone disposition / design evidence |
|---|---|
| F-01 | M2: durable job/outcome query after restart, matching input/attempt identity; F17 and state table |
| F-02 | M2: malformed/schema/oversize rejected before new job or handler; F09/F14; parse limits D7 pending |
| F-03 | M2: absent server task rejected; valid catalog but no live handler stays QUEUED; removal after acceptance cannot silently rewrite schema; assigned local mismatch guarded failure; F10/F14/F17 |
| F-04 | M2: handler throws → FAILED, no automatic business retry; effect-after-error marked uncertain, independent recorder; F10 |
| F-05 | M2: scoped same command returns one job, changed command conflicts; no once-only effect inference; F01 |
| F-06 | M2: response lost after commit replays same identity; before receipt loss causes normal same-key acceptance; F02 |
| F-07 | M2: precommit rollback leaves neither job/key, postcommit persists QUEUED for future poll; no compatible available worker means no finite completion bound; F02/F11/F17 |
| F-08 | M2: accepted completion replay returns immutable receipt even after old expiry, authoritative query/restart; F07/F17 |
| F-09 | Move conditional M3 minimum to M2 if D5 accepted: two workers/pull requests claim, not two scheduler pushers; one current grant; F03/F04 |
| F-10 | Move minimum to M2: stale A heartbeat/completion rejected after B, observe effects separately; F05 |
| F-11 | Move minimum to M2: crash before invoke or after effect, bounded ownership requeue/exhaustion with uncertainty; F08/F12 |
| F-12 | Adapt minimum to pull M2: server crashes after claim commit/before claim response, then replay/recovery; scheduler recovery scan crash reruns conditional scan; no push-dispatch checkpoint invented; F04/F08/F11 |
| F-13 | Move minimum to M2: <, =, > expiry post-lock DB clock and real-time pause/renew/recover; jump/skew assumptions documented, no universal clock guarantee; F05/F06 |
| F-14 | M4 deferred: durable business retry/backoff/history/late-trigger oracle requires new accepted design; M2 ownership budget F12 is different |
| F-15 | M4 timeout precedence deferred; M2 SDK request/drain deadline does not cancel job, F15 |
| F-16 | M4 cancel transition deferred; M2 graceful intake/drain resource verification F15 only |
| F-17 | Conditional M2 if D2 broker, otherwise M5: state+intent rollback/commit durability contract must be designed first |
| F-18 | Conditional M2 if broker, otherwise M5: stable event identity/duplicate publish checkpoint contract first |
| F-19 | Conditional M2 if broker, otherwise M5: consumer internal dedup/ACK boundary first; external effect uncertainty already F08 |
| F-20 | M5 under pull: ordering/poison/DLQ/redrive pending; broker-first changes prerequisite scope, no N/A/pass claim |
| F-21 | DB-outage/unknown-commit portion M2 F11; publish/ACK portion conditional broker/M5; observable retry budget D7 pending |
| F-22 | Minimal config/codec/start/reconnect/drain and consumer build M2 F14/F15; starter/extended support matrix M6; supported versions not established |
| F-23 | M7 DAG/fan-in deferred; no fabricated oracle |
| F-24 | Minimal M2 denied/auth/secret-sentinel/correlation/no unbounded metric labels F09/F16; fuller deployment threat model M8 |
| F-25 | M9 workload/budget pending; require raw workload/fault/state/effect/latency/error evidence, no results claimed |

## Reviewer, Release and Coordinator disposition

Reviewer source approves only bootstrap 9a45b11 and planning d7d7de6 documents, separately. CN-01 boundary question maps D1/D3 consumer build; CN-11 unauthorized completion/oversize/serialization/shutdown maps D6/D7/D8 F09/F14–F16; CN-12 races/performance maps D4/F01–F08 and pending Q13. No finding or approval is invented for the Architect draft. Revised design needs a newly assigned exact-revision review; assembled candidate approval remains separate.

Release source requests exact config mapping and runnable build/test/artifact boundaries. D1 supplies explicit POM/wrapper/CI/compose/ops paths; proposed artifact boundaries are server executable JAR and SDK library JAR, parent version configured in Release-owned root POM (no tag/package publishing grant). Real DB/fault-process test capability is required; containers are conditional on available environment, not assumed installed. Clean developer/CI commands and exact dependency/toolchain pins must be supplied by Release after accepted direction. Initial develop setup/staging plan is plan-only, published; no candidate/setup execution is asserted. No architectural decision is necessary merely to review that document plan, but build implementation still depends on D1/D3.

Coordinator source governs task state, explicit assignments and GOV-003/004. Naming is resolved, Release role is available for planning and PM receipt resolves role-publication blockers. Actual develop establishment/M0 build and M2 assignment remain pending. Architect requests Coordinator replan D5 only after Owner approval and attach this exact revision/evidence; does not rewrite board/state.

## Blocking questions / what Owner can decide now

Ready for direction: D1 exact mapping; D2 pull/store versus broker; D3 Java/build direction; D4 semantics; D5 minimal recovery scope; D6 public behavior/equality/compatibility; D8 single-owner/auth direction. They remain PROPOSED_NOT_ACCEPTED, and approving direction does not establish exact versions, executed tests or implementation assignment.

Still needs concrete data: (1) target SDK JDK/Spring client versions and support duration; (2) typical/max payload/result, task duration, concurrency/backlog and expected arrivals; (3) tolerated outage/pause/drain and maximum client replay/redrive horizon, plus sensitive outcome retention; (4) loopback-only demo versus exposed network, credential distribution/rotation/revocation requirements; (5) future latency/SLO/load-run budget. D7 numbers cannot be presented as evidence-backed defaults without these answers. No approval of an uncertain risk is inferred from silence.

The reconciled packet can now be presented for Owner choices; unresolved data blocks final policy/version acceptance, not this preparation deliverable. All executable tests NOT_RUN. Publication of revised documentation is not architecture acceptance or combined-candidate approval.
