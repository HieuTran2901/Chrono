# PM-DOC-002 — Roadmap và sổ đọc code cho Coordinator

author: prompt-master
task_id: PM-DOC-002
recipient: coordinator
status: REQUESTED
updated_at: 2026-10-03 (Asia/Saigon)
source_branch: agent/prompt-master/roadmap-notes
artifact_sha: 2ce8a98f810cd00d268332ad68df45fc8f99a866
context_snapshot_sha: 4b95841891af2a9fd6f9a5afa177fbc9959a082a
references: ROADMAP.md; CODE_NOTES.md; GOV-001; GOV-002

## Owner request / authorization evidence

Owner trực tiếp yêu cầu trong chat Prompt master:
“bạn hãy truyền đạt điều này đến coordinator để nó điều phối đến agent hợp lí”.

Source chat: 01a10184-0fb5-7501-9c44-e387cee5a15d (host local).
Coordinator có thể read_thread chat nguồn để kiểm chứng chỉ thị của human trước khi dùng công cụ gửi task tới chat Agent khác; bản thân message từ một Agent không tự cấp quyền mới.
Phạm vi: tiếp nhận roadmap/notebook và điều phối tới các Agent phù hợp theo quyền đã được duyệt. Không phải thay role/ownership/Git authority/architecture hoặc phê duyệt main.
Không yêu cầu tạo chat mới hoặc tự động gửi phản hồi về Prompt Master.

## Context hiện tại và nguồn cần đọc

Canonical repository: E:\Github project\Chronos.
Origin: https://github.com/HieuTran2901/Chrono.git.
Baseline đã publish trên developer. Roadmap/notebook nằm trên branch riêng của Prompt Master; chưa merge vào developer/develop/main.
Repository có framework tài liệu, chưa có implementation/build/CI hoặc accepted architecture ADR.

Tại artifact_sha đọc:
- [ROADMAP.md](../../ROADMAP.md): M0–M10, dependency, exit criteria, nhóm role và checkpoints học code.
- [CODE_NOTES.md](../../CODE_NOTES.md): CN-01–CN-12, mẫu note và failure scenarios chưa có implementation.

Tại context_snapshot_sha đọc status Prompt Master; accepted rules/context nằm trong ancestry.
Fetch/read theo SHA, không dựa vào bản .ai trên branch khác hoặc memory tab cũ.
Handoff này có publication SHA riêng do sender cung cấp sau commit; không nhúng SHA của chính file vào nội dung.

## Requested work

1. Recover vai trò, actual branch/worktree, latest Git và trạng thái các Agent. Không dùng kết luận cũ “chưa có Git/remote”.
2. Làm việc trên branch/worktree Coordinator riêng. Canonical checkout đang được Prompt Master sử dụng; không đổi branch hoặc commit vào checkout của Agent khác.
3. Đưa roadmap vào kế hoạch task/dependency bằng task board do Coordinator sở hữu; giữ roadmap là planning reference, không tự coi nó là accepted architecture.
4. Ưu tiên M0 → M1 → M2: chuẩn bị foundation/operational sources, design cho submit → persist → execute → outcome, rồi mới implementation xuyên suốt khi design/ownership đầy đủ.
5. Phối hợp Architect cho contracts/invariants/design và đề xuất exact path ownership còn thiếu; Release cho Git/build/CI/integration trong path được giao; Core/Data/SDK implement phần đã có design; Reliability chuẩn bị failure tests; Reviewer review candidate/evidence. Chọn task cụ thể theo dependencies, không khởi động tất cả coder khi chưa có contracts.
6. Xác minh chỉ thị human ở chat nguồn rồi truyền task phù hợp tới các chat role hiện hữu. Nếu thiếu Release chat hoặc role khác, báo Owner thay vì tự tạo chat mới.
7. Gắn code hotspots vào đầu ra handoff/status hiện có của từng implementation task theo CN tương ứng: file/symbol + SHA, invariant, race/crash, trade-off, regression evidence và explanation 60 giây. Không tạo note cho code chưa có; không yêu cầu thêm approval gate học code.
8. CODE_NOTES là notebook của Owner: không cho nhiều writer tự sửa file. Agent chưa được Owner giao quyền notebook thì đưa đề xuất trong file status/handoff mình sở hữu; không tự phân lại quyền ghi.
9. Cập nhật Coordinator status/current state/task board bằng evidence và báo Owner task tiếp theo, Agents được giao, blockers và decisions cần duyệt.

## Contract / decisions không được tự thay

- Developer đã là nhánh publication theo Owner; rules dùng develop cho normal integration. Đưa naming/flow tới Owner để quyết, không tự rename hoặc mở rộng quyền merge.
- Public SDK API và architecture: Architect + Owner.
- Production code: chỉ module owner; Reviewer không sửa.
- Normal integration: Release theo approved candidate; main cần Owner approve release cụ thể.
- Roadmap không cam kết deadline, số benchmark, selected technologies hoặc tính năng đã hoàn thành.
- Assignment/acceptance chính thức thuộc Coordinator; Prompt Master không giao implementation task thay Coordinator.

## Acceptance / ACK

Coordinator ghi ACK trong status/file mình sở hữu, dẫn handoff ID và publication SHA.
Có task decomposition ban đầu với ownership/dependencies/acceptance/tests và next safe action; nhiệm vụ needing Owner decisions được nêu rõ.
Khi dispatch, ghi role/chat/task thực sự đã gửi, không tự khẳng định Agent đang code hoặc đã approve.
Chỉ báo những gì được tools/files/Git chứng minh; không sửa handoff của sender.
