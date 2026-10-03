# Architecture navigation

author: architect
task_id: M0-ARCH-001; M1-DESIGN-001
status: DRAFTS_AVAILABLE; NO_ACCEPTED_ARCHITECTURE
updated_at: 2026-10-03 20:10 +07:00 (Asia/Saigon)
source_branch: agent/architecture/m1-foundation

Architecture Source of Truth remains accepted documents under docs/architecture/ and docs/design/, with ADRs for accepted decisions. This page only indexes author drafts; it does not approve them or duplicate the specification.

| Document | Status / purpose | Decision reference |
|---|---|---|
| [M1 foundation](../../docs/architecture/m1-foundation.md) | DRAFT — module direction, runtime/build/dependency alternatives, scope | ARCH-PROP-001 D1–D8 |
| [M1 first job](../../docs/design/m1-first-job.md) | DRAFT — proposed domain, atomic ports, wire/errors, failure timelines/test expectations | ARCH-PROP-001 D4–D8 |
| [ARCH-PROP-001](../proposals/ARCH-PROP-001-m1-foundation.md) | PROPOSED — exact missing ownership and Owner decision checklist | Owner acceptance pending |

No accepted ADR or architecture/API approval evidence exists at this draft revision. GOV-003 at efca8bc9c1758848bce9c7721f19adfdca651277 resolves develop/developer naming only. Assignment/operational truth is Coordinator's published context at 4a18310a0af1f982ab326b77c496b4721fd8ef18 and subsequent published revisions; bootstrap context copies in this worktree are historical. Author recovery/ACK/evidence: [Architect status](../status/architect.md). No application implementation, benchmark or executable test result is asserted.
