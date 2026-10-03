# Owner decision index

Owner: Coordinator. This index records decisions; it does not create authority.

## GOV-001 — AI framework baseline

- Status: ACCEPTED.
- Date: 2026-10-03, Asia/Saigon; exact approval time not recorded.
- Decision maker: human Owner.
- Evidence: Owner message: `E:\Github project\Chronos` followed by “tôi duyệt cho bạn, và hãy chuyển khung làm việc vào đây”.
- Approved: P1 role/ownership/Source of Truth; P2 structure/publish-read/recovery; P3 review/integration gates including Release-only isolated candidate staging.
- Scope: one-time documentation scaffold initialization by Prompt Master; ongoing ownership is defined in collaboration.md.
- Proposal: [PM-001](../proposals/PM-001-agent-system.md).
- Sources: [collaboration.md](../rules/collaboration.md), [git.md](../rules/git.md), [feature.md](../workflows/feature.md), [recovery.md](../workflows/recovery.md).
- No architecture, public SDK API, production feature, main release or deployment approval is implied.

## GOV-002 — Initial GitHub publication to developer

- Status: ACCEPTED / initial publication VERIFIED.
- Date: 2026-10-03, Asia/Saigon; exact instruction time not recorded.
- Decision maker: human Owner.
- Evidence: Owner supplied https://github.com/HieuTran2901/Chrono.git and instructed “bạn hãy tiến hành sử dụng git remote sau đó push code lên nhánh developer”.
- Scope: Prompt Master may initialize Git, configure that origin, commit the existing approved framework on its own bootstrap branch and push it to developer. Synchronizing bootstrap metadata with observed Git evidence is included; ongoing role ownership remains unchanged.
- Local branch: agent/prompt-master/bootstrap. Remote target: origin/developer.
- Verified initial commit: fee3c7e0e6b661308d1e09005e8f188889068e8e; remote developer equaled local HEAD after successful push.
- No independent Reviewer APPROVED status is claimed. This is initial publication of an Owner-approved documentation scaffold, not feature integration under the normal workflow.
- Bootstrap task completion means published to the explicitly requested developer branch; ordinary feature DONE still means validated develop integration.
- This does not establish developer as a permanent replacement for develop, grant recurring shared-branch authority, or authorize main/deployment.
- Publication receipt/status: [prompt-master.md](../status/prompt-master.md).

Preserve future rejected/superseded decisions with their proposal/ADR references. These records refer to earlier publication evidence; they do not embed the SHA of their own metadata commit.

## COORD-001 — Roadmap orchestration authorization

- Status: ACCEPTED (scope: dispatch/coordination only).
- Date: 2026-10-03, Asia/Saigon.
- Decision maker: human Owner.
- Evidence verified by read_thread: Prompt master chat 01a10184-0fb5-7501-9c44-e387cee5a15d, user turn 01a101d0-d3f9-7220-a142-9f785b02d8cd: “bạn hãy truyền đạt điều này đến coordinator để nó điều phối đến agent hợp lí”.
- Scope: Coordinator decomposes roadmap and dispatches appropriate existing Agent chats within GOV-001 ownership. Does not accept architecture/API, change roles/paths/Git authority, create chats or authorize main.
- Source handoff: d7d7de6d23a8951d6094a510eb90ec99895550fd:.ai/handoffs/PM-DOC-002-prompt-master-to-coordinator-roadmap.md.
- Follow-up: branch naming resolved by GOV-003 below; Release activation and exact missing path mapping remain pending.

## GOV-003 — Preserve develop for normal integration

- Status: ACCEPTED.
- Date: 2026-10-03, Asia/Saigon.
- Decision maker: human Owner.
- Evidence: direct answer in Coordinator chat 01a101ab-edcc-7870-943d-8dcfa48473d4: “Giữ develop theo GOV-001; developer chỉ là nhánh bootstrap.”
- Normal integration target remains develop under GOV-001. Developer is only the GOV-002 bootstrap publication branch.
- No rename/deletion of developer, recurring push authority change, main release or deployment is authorized.
- Release must establish the actual develop workflow under existing authority after activation and appropriate evidence gates. Branch setup has not been performed by Coordinator.
- Related task: M0-GOV-001. Naming blocker resolved; Release activation remains pending.

## GOV-004 — Consolidate and publish completed Agent documentation via Prompt Master

- Status: ACCEPTED (bounded publication instruction).
- Date: 2026-10-03, Asia/Saigon.
- Decision maker: human Owner.
- Direct evidence in Coordinator chat 01a101ab-edcc-7870-943d-8dcfa48473d4: “tất cả các agents hiện tại đã hoàn thành, nhưng chưa có quyền để push lên github, bạn hãy điều phối các agents này tổng hợp sau đó báo cáo với prompt master để tiến hành push lên git”.
- Scope: Coordinator gathers final local manifests; Prompt Master proceeds with Git publication of these completed Agent documents to the established Chrono origin on exact listed author branches, with receipts.
- Manifest: COORD-PUB-001 (../handoffs/COORD-PUB-001-coordinator-to-prompt-master.md); immutable handoff SHA supplied after commit.
- No permanent role/ownership/Git-rule change, design/API acceptance, develop/main integration, force-push, deployment or artifact publishing inferred.
- Earlier automatic-review publication rejections remain historical evidence; no successful publication is claimed for this batch until remote verification.
