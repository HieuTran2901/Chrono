# Architecture navigation

author: architect
task_id: M0-ARCH-001; M1-DESIGN-001
status: INPUTS_RECONCILED; PROPOSED_NOT_ACCEPTED
updated_at: 2026-10-03 21:13 +07:00 (Asia/Saigon)
source_branch: agent/architecture/m1-foundation

Architecture Source of Truth remains accepted documents under docs/architecture/ and docs/design/, with ADRs for accepted decisions. This page only indexes author drafts; it does not approve them or duplicate the specification.

| Document | Status / purpose | Decision reference |
|---|---|---|
| [M1 foundation](../../docs/architecture/m1-foundation.md) | DRAFT — module direction, runtime/build/dependency alternatives, scope | ARCH-PROP-001 D1–D8 |
| [M1 first job](../../docs/design/m1-first-job.md) | DRAFT — proposed domain, atomic ports, wire/errors, failure timelines/test expectations | ARCH-PROP-001 D4–D8 |
| [ARCH-PROP-001](../proposals/ARCH-PROP-001-m1-foundation.md) | PROPOSED — exact missing ownership, immutable input manifest/dispositions, ready direction choices and unanswered data questions | Owner acceptance pending |

No accepted ADR or architecture/API approval evidence exists at this draft revision. GOV-003 at efca8bc9c1758848bce9c7721f19adfdca651277 resolves develop/developer naming only. Assignment/operational truth is Coordinator's published context at 4a18310a0af1f982ab326b77c496b4721fd8ef18 and subsequent published revisions; bootstrap context copies in this worktree are historical. Author recovery/ACK/evidence: [Architect status](../status/architect.md). No application implementation, benchmark or executable test result is asserted.

PM-ARCH-001 at fd56daf45288a4d4c84d6c81da553bc0bee997ec requests reconciliation before Owner decisions. Full role inputs and their statuses have been read; source-linked disposition exists only in ARCH-PROP-001. PM-PUB-001 receipt at f4eb0e3d1a97cf8f42900d6dc06ef3249897d0a8 resolves batch publication blockers; Coordinator operational source 7834520200abb24738feb55a5923518c23c4fc51 verifies Release planning availability. Accepted architecture, exact parameter/version policy and combined candidate review/integration remain pending.
