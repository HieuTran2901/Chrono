# Technical baseline

Owner: Architect. Initial content preserves the original workflow's technical requirements; this is not an architecture ADR or permission to implement.

Before code, explain WHY, WHAT, FLOW, FAILURE and TRADE-OFF for the assigned task.
Use accepted design invariants and explicit domain transitions; do not set arbitrary status strings or introduce entities because names sound plausible.
Record races, transaction assumptions, repository/event contracts and failure cases.
Do not assume exactly-once delivery. Validate duplication, crash/restart, stale/expired leases and concurrent schedulers/workers against the accepted design.
Do not hold database transactions open across external calls.
Document an index's query, columns, selectivity, read benefit and write cost.
Schema changes need migration, compatibility/rollback consideration and tests. Do not edit released migrations.
Messaging contracts need producer/consumer, key/ordering/retention, retry/duplicate/failure semantics.
SDK must not leak storage rows, Kafka internals or internal scheduler/server classes.
Architecture, database architecture, messaging strategy, concurrency model, major dependencies, module boundaries and public SDK API changes require a proposal and the appropriate Architect + Owner decision.

Coder owns unit tests in their module. Reliability owns cross-module/failure test paths.
Tests must exercise behavior and accepted invariants; use real infrastructure when required by the task's distributed semantics.
Do not invent build commands, dependency versions, selected infrastructure or performance results. Report unavailable environments as unverified.
No production coding style, package structure, Java version, build tool or API signature is established by this bootstrap.
