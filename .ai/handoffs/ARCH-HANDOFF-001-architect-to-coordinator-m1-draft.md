# ARCH-HANDOFF-001 — M0 ownership proposal and M1 first-job draft

author: architect
recipient: coordinator; Core/Data/SDK/Reliability/Reviewer consume through Coordinator
task_id: M0-ARCH-001; M1-DESIGN-001
status: INPUTS_RECONCILED_FOR_OWNER_DIRECTION; PROPOSED_NOT_ACCEPTED
updated_at: 2026-10-03 21:13 +07:00 (Asia/Saigon)
source_branch: agent/architecture/m1-foundation
references: COORD-ROAD-001 at 4a18310a0af1f982ab326b77c496b4721fd8ef18; GOV-003 at efca8bc9c1758848bce9c7721f19adfdca651277

## Context / source revisions

ACK assignment and GOV-003 in [author status](../status/architect.md). Baseline 9a45b111847685255cf1006a2b839d31ecdf31a4; roadmap/notebook planning source 2ce8a98f810cd00d268332ad68df45fc8f99a866. Authored documents are on agent/architecture/m1-foundation. Consumer must resolve successful published SHA from origin/chat receipt and read SHA:path, not merge for reading. No own-publication SHA is embedded in this commit.

Deliverables: [foundation](../../docs/architecture/m1-foundation.md), [domain/wire/failure design](../../docs/design/m1-first-job.md), [ARCH-PROP-001 decision checklist](../proposals/ARCH-PROP-001-m1-foundation.md), [navigation](../context/ARCHITECTURE.md), author status.

## Requested work and acceptance

Coordinator: link actual publication SHA/task evidence, bring D1–D8 to Owner, arrange exact-draft review and publish input references. Keep M0/M1 DESIGNING until acceptance; do not dispatch M2 production. If Owner accepts D5, move minimal ownership expiry/renew/recovery into M2 dependency plan. GOV-003 naming is resolved; Release activation/develop setup remains pending. Do not apply proposed mapping without Owner decision and maintained rules.

Core: check Job/Attempt transitions and port boundaries, DB-clock validity after lock wait, receipt precedence and recovery budget. Data: validate atomic arbitration for submit/acquisition keys, claim/renew/complete/recover lock order, indexes against actual queries, retention/GC and canonicalization upgrade. SDK: evaluate proposed HTTP errors/keys, explicit codecs, fresh sessions, free-capacity pull, stable replay and shutdown. Reliability: map F01–F13 to deterministic injection/observations with real DB/HTTP/worker/sink; future timeout/cancel/broker tests remain contract-TBD. Reviewer: check exact draft consistency, authority and failure guarantees; do not approve future code from document inspection.

Acceptance of this preparation: traceable draft, exact missing-path proposal, Owner choices, failure timelines, test expectations, source/ACK/limits and CN hotspots. Acceptance of architecture/API requires explicit Owner evidence and reconciled inputs; tests alone cannot approve it. No accepted ADR exists yet; record accepted ADRs after decision, not now.

## Contracts / risks / evidence

Core/Data semantic ports: SubmitOrReplay, ReadJob, ClaimOrReplay, Renew, CompleteOrReplay, RecoverExpired. I1–I8 specify durable identity, valid lifecycle, conditional ownership, receipt replay, short transactions, bounded at-least-once recovery and authorized boundaries. Pull has no broker publish/ACK dependency; selecting broker invalidates that scope and requires event-intent/ACK/dedup design before implementation. Opaque wire credentials are distinct from persistence rows/public client query models.

Risks: lease recovery may repeat external effects; polling load/latency unmeasured; limits/retention are starter proposals; exact dependency versions and security exposure not accepted. Module/POM mapping is proposed, no expanded writes occurred. Application/tests/benchmarks NOT_RUN; no executable commands or pass results exist. Document-only checks and push result follow in publication receipt/status.

CN-01/02/03/06/09 are mapped to draft sections/invariants/failure timelines in author status. Code/symbol/implementation SHA and regression evidence remain TODO/NOT_RUN; 60-second explanation is a proposed model, not an implementation walkthrough. Owner notebook untouched.

## Reconciled handoff — PM-ARCH-001

ACK PM-ARCH-001 at fd56daf45288a4d4c84d6c81da553bc0bee997ec; PM publication receipt f4eb0e3d1a97cf8f42900d6dc06ef3249897d0a8 resolves input-publication blockers. The immutable per-role SHA/path manifest and all dispositions are in ARCH-PROP-001, not repeated here. Core/Data/SDK/Reliability full inputs and author statuses, Reviewer scope, Release plan and Coordinator state were read. Release planning role is now available; prior requests for activation/publication reflect the original draft snapshot. Actual develop setup/build still pending.

Current packet: proposal records agreement versus Owner alternatives, maps every material contract question/test row, and separates ready direction from data gaps. Design corrects acquisition-key/job lock ordering, receipt-first schema/catalog replay, precise DB-time boundary/attempt count, query consistency, unknown handler/codecs, readiness/shutdown/resource ownership and numeric/schema compatibility. Recommend minimal bounded expiry/renew/fencing/recovery in M2; advanced M3 and M4 remain later. This is a proposed dependency change, not a Coordinator-board edit or accepted guarantee.

Coordinator: consume the new successfully published revision supplied in chat/status after commit; link Owner direction decisions to exact SHA and request author/Reviewer recheck on changed contract. D1/D2/D3/D4/D5/D6 and D8 direction can be chosen now. D7 defaults and exact D3 versions/support matrix require concrete workload/outage/replay/drain/client/exposure context listed in proposal. Do not interpret preparation completion as accepted design, READY_TO_MERGE or integrated DONE. Existing Reviewer approvals cover only their baseline/planning components; this revised design and any staged candidate are new review targets.

Requested follow-ups after direction acceptance: Data confirms cycle-free lock graph/SQL/receipt storage and migration/GC envelope; Core confirms state/time/attempt policy; SDK confirms JCS/codec/error/lifecycle fixtures and public Java surface; Reliability derives executable oracles from F01–F17 and scoped F-01–25 with accepted time bounds; Release supplies exact build/dependency/toolchain/consumer/test-environment manifest and real entrypoints. Coordinator assigns exact paths/acceptance for these follow-ups. All application tests NOT_RUN; no test/performance/compatibility pass claimed. No cross-chat tool message sent.
## Verified reconciled component

Published document component: 70d81795d6372032cd2296b0d2dcdf9113598b71 on origin/agent/architecture/m1-foundation; push SUCCESS and exact ls-remote equality verified. Read that SHA:path for revised proposal/design and all input dispositions. Later receipt changes only Architect status/this handoff; it does not alter the decision specifications. Six-file scope/link/metadata, immutable source/ID coverage and staged whitespace checks PASS; application tests NOT_RUN. Source-linked preparation complete; D1–D8 still PROPOSED_NOT_ACCEPTED. Coordinator can collect Owner choices and schedule exact revised-document review/build/version/policy follow-ups; no dependent production task or acceptance is asserted.
