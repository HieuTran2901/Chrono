# ARCH-HANDOFF-001 — M0 ownership proposal and M1 first-job draft

author: architect
recipient: coordinator; Core/Data/SDK/Reliability/Reviewer consume through Coordinator
task_id: M0-ARCH-001; M1-DESIGN-001
status: DRAFT_OUTPUT_FOR_DECISION; NOT_READY_FOR_IMPLEMENTATION
updated_at: 2026-10-03 20:10 +07:00 (Asia/Saigon)
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
