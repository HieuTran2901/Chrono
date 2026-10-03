# ARCH-PROP-001 — missing ownership and M1 foundation decisions

author: architect
task_id: M0-ARCH-001; M1-DESIGN-001
status: PROPOSED_NOT_ACCEPTED
updated_at: 2026-10-03 20:10 +07:00 (Asia/Saigon)
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

Owner can approve D1–D8 independently or request changes. Acceptance must name the immutable draft revision and concrete approved choices; Coordinator records evidence and Prompt Master updates ownership rules only after approval. Architect then records accepted ADR(s) and reconciles role feedback; do not pre-create ACCEPTED ADRs. M0 exit requires Release activation/develop setup, accepted exact version manifest, runnable build/test/CI evidence and ownership. M1 exit requires accepted contracts plus reconciled Core/Data/SDK/Reliability input. Current deliverable is a published draft, not READY to implement, Reviewer APPROVED or integrated DONE.
