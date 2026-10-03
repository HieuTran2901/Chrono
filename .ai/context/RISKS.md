# Risks

author: coordinator
updated_at: 2026-10-03 19:56 +07:00 (Asia/Saigon)
source_branch: agent/coordinator/project-state

| ID | Risk / affected roles | Handling |
|---|---|---|
| R01 resolved initial publication | Git/remote absence claims are stale | Baseline 9a45b11 observed origin/developer after fetch; GOV-002 |
| R02 open | Chats remain bound to historical C: path | Explicit E: workdir; isolated role worktrees; never switch shared checkout |
| R03 open | No accepted architecture or application | Preparation only; M2 blocked until Architect + Owner decisions |
| R04 open | Server API/shared contracts/root build/CI/operations/docs ownership missing | M0-ARCH-001 proposes exact paths; Owner accepts; no implicit write grants |
| R05 open | Independent bootstrap/planning review missing | M0-REVIEW-001 assigned; no APPROVED/integrated claim |
| R06 open | developer publication vs develop normal integration | M0-GOV-001 for Owner decision; no automatic rename or authority expansion |
| R07 open | Release role absent from inspected chat list | Ask Owner to provide/activate Release chat; Coordinator does not substitute or create chat |
| R08 handled | Notebook may become multi-writer or contain fake code evidence | Owner notebook; Agents record hotspots in own status/handoff; TODO until code exists |

Do not copy review findings here; reference author report and immutable SHA when published.