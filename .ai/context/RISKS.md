# Risks

author: coordinator
updated_at: 2026-10-03 20:32 +07:00 (Asia/Saigon)
source_branch: agent/coordinator/project-state

| ID | Risk / affected roles | Handling |
|---|---|---|
| R01 resolved initial publication | Git/remote absence claims are stale | Baseline 9a45b11 observed origin/developer after fetch; GOV-002 |
| R02 open | Chats remain bound to historical C: path | Explicit E: workdir; isolated role worktrees; never switch shared checkout |
| R03 open | No accepted architecture or application | Preparation only; M2 blocked until Architect + Owner decisions |
| R04 open | Server API/shared contracts/root build/CI/operations/docs ownership missing | M0-ARCH-001 proposes exact paths; Owner accepts; no implicit write grants |
| R05 component review complete | Baseline/planning reviewed individually | Local report 6b693a8: both document APPROVED; no combined candidate/integration approval |
| R06 resolved naming | developer publication vs develop normal integration | GOV-003: Owner confirms develop; developer bootstrap only. Actual flow setup tracked by M0-RELEASE-PLAN-001 |
| R07 resolved availability | Release role absent in earlier snapshot | Owner added RELEASE & INTEGRATION ENGINEER; verified chat 01a101de-81d0-7822-b019-5c4e2d0f90f5; operational plan assigned |
| R08 handled | Notebook may become multi-writer or contain fake code evidence | Owner notebook; Agents record hotspots in own status/handoff; TODO until code exists |
| R09 publication blocker | Six completed role batches blocked by earlier auto-review export checks | GOV-004 appoints PM publication; exact manifest COORD-PUB-001; retain rejected push history and verify remote before resolving |

Do not copy review findings here; reference author report and immutable SHA when published.
