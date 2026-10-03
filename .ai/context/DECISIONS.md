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
