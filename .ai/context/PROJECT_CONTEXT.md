# Project context

Owner: Coordinator. Updated: 2026-10-03 (Asia/Saigon).
Canonical folder: `E:\Github project\Chronos`.

Chronos is intended to be a distributed background job and workflow platform for Java/Spring Boot.
Target responsibilities from the Owner's workflow: scheduling, distributed execution, worker coordination, retry, timeout, heartbeat, leasing, failure recovery, idempotency, messaging/outbox/DLQ, observability and performance.
The fluent SDK example and dependency coordinates in the original workflow describe desired developer experience; no public SDK implementation or formal API approval exists yet.

Owner wants code that is correct, understandable, testable and consistently coordinated, with explainable failure behavior and trade-offs.
Prompt Master organizes cooperation and does not substitute for Coordinator, Architect, coders, Reviewer or Release.

Observed implementation: none in this folder. No accepted ADR, build tool/version, schema, server API, selected dependency versions or deployed infrastructure is present.
PostgreSQL/Kafka and other technologies named in the original workflow are design inputs for Architect + Owner to validate, not new decisions made by Prompt Master.
Governance approval: [DECISIONS.md](DECISIONS.md).
