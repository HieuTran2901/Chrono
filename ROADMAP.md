# Chronos — Roadmap hoàn thiện dự án

Ngày lập: 2026-10-03 (Asia/Saigon). Người soạn: Prompt Master, theo yêu cầu Owner.
Baseline: `9a45b111847685255cf1006a2b839d31ecdf31a4`, đã xuất bản trên `origin/developer`.
Trạng thái: kế hoạch đề xuất theo milestone; không phải task assignment, thiết kế được chấp nhận hoặc cam kết deadline.

## Điểm xuất phát và cách dùng

Đã có khung phối hợp Agent, Git remote và baseline tài liệu. Chưa có application code, build, CI, schema, SDK hay ADR được chấp nhận.
Nguồn trạng thái thực tế: [.ai/context/CURRENT_STATE.md](.ai/context/CURRENT_STATE.md).
Nguồn task/assignment thực tế: [.ai/context/TASK_BOARD.md](.ai/context/TASK_BOARD.md).
Quyền và ownership: [.ai/rules/collaboration.md](.ai/rules/collaboration.md).

Roadmap trả lời “xây gì trước, chứng minh bằng gì”; Coordinator chuyển từng mốc thành task cụ thể, dependencies và branch.
Tên nhóm Agent bên dưới chỉ mô tả người tham gia theo role hiện có, không tự phân lại ownership.
Architect + Owner chốt design, public API, dependency và boundary trước implementation tương ứng.
Đọc và điền [CODE_NOTES.md](CODE_NOTES.md) song song, tập trung vào phần code thực sự khó.

Không gắn ngày hoàn thành giả định. Sau mốc nền tảng và luồng xuyên suốt đầu tiên, Coordinator dùng effort thực tế để lập lịch với Owner.

## Các mức sản phẩm

| Mức | Khi đạt | Owner có thể chứng minh |
|---|---|---|
| Demo đầu tiên | M2 | Submit một job, worker thực thi, tra cứu kết quả; giải thích luồng dữ liệu và transaction |
| Job platform MVP | M6 | Tích hợp qua SDK/starter; scheduling, recovery, retry, timeout/cancel, idempotency và messaging đã kiểm thử theo contract được duyệt |
| Feature complete theo mục tiêu hiện tại | M7–M8 | Workflow/DAG và vận hành/security có phạm vi được chốt, không chỉ happy path |
| Release candidate | M9–M10 | Failure/load evidence, tài liệu, compatibility và release gates đủ; main vẫn cần Owner duyệt |

“MVP” là mốc phạm vi đề xuất, không phải bằng chứng đã đủ điều kiện production. Owner quyết phạm vi phát hành thật.

## Thứ tự và phụ thuộc

`Baseline → M0 → M1 → M2 → M3 → M4 → M5 → M6 → M7 → M8 → M9 → M10`

Đây là thứ tự ưu tiên. Thiết kế API, telemetry, security và failure tests bắt đầu từ M1–M2.
Coordinator có thể cho docs/test preparation chạy song song khi contracts/ownership đã rõ; không song song hóa code dùng chung chưa ổn định.
Nếu thiết kế đã duyệt yêu cầu messaging/outbox ngay trong luồng M2, đưa phần tối thiểu của M5 vào M2 và cập nhật dependency; không tạo một transport tạm chỉ để đúng thứ tự bảng.

| Mốc | Kết quả cần có | Phụ thuộc | Nhóm tham gia | Điều kiện ra khỏi mốc | Chủ đề note |
|---|---|---|---|---|---|
| M0 | Workflow Git và project foundation chạy thật | Baseline | Coordinator, Release; Architect cho boundaries | Integration branch rõ, path ownership đầy đủ, build/test/CI có lệnh thật | CN-01 |
| M1 | Thiết kế nền tảng và contracts cho job đầu tiên | M0 | Architect + Owner; Core/Data/SDK góp yêu cầu | Invariants, state/transaction/failure semantics và public contract cần thiết được chấp nhận | CN-02, CN-03, CN-06 |
| M2 | Luồng job bền vững xuyên suốt | M1 | Core, Data, SDK; Reliability, Reviewer, Release | Submit → persist → execute → query outcome chạy được, có negative tests | CN-02, CN-06, CN-09 |
| M3 | Phân phối execution, lease/heartbeat và recovery | M2 | Core, Data, SDK; Reliability, Reviewer | Concurrency và worker/scheduler crash đúng semantics đã duyệt; stale owner không ghi đè state | CN-03, CN-04 |
| M4 | Scheduling nâng cao, retry, timeout, cancellation | M3 | Core, SDK, Data; Reliability, Reviewer | Race giữa completion/retry/timeout/cancel được test; attempt history giải thích được | CN-05 |
| M5 | Messaging/outbox, idempotency và DLQ | M2–M4, contracts đã duyệt | Data, Core, SDK; Reliability, Reviewer | DB/message crash windows, duplicates/lost ACK và poison events được kiểm chứng | CN-06, CN-07, CN-08 |
| M6 | Java SDK, Worker SDK, Spring Boot starter hoàn chỉnh | M2–M5 | SDK; Core/Data theo handoff; Reviewer | App ví dụ tích hợp được, lifecycle/reconnect/config/error mapping và compatibility được test | CN-09 |
| M7 | Workflow/DAG execution có recovery | M3–M6 | Architect + Owner; Core/Data/SDK; Reliability | Dependency, fan-out/fan-in, failure/retry/cancel/restart của workflow đúng design | CN-10 |
| M8 | Observability, security và vận hành | Nền tảng đã làm từ M1–M2; hoàn thiện sau M7 | Các module owner; Release config được giao; Reviewer | Trace/metrics/logs/runbook hữu ích; threat model và security acceptance được kiểm chứng | CN-11 |
| M9 | Chaos, load và tối ưu theo evidence | M3–M8 | Reliability; code owners fix; Reviewer | Pass failure matrix; workload/budget được Owner chốt; đo trước/sau rõ, không benchmark giả | CN-12 |
| M10 | Tài liệu, demo, review tổng thể và release | M9 | Coordinator, Reviewer, Release, Owner | Candidate được approve/test, demo tái lập, release notes/risks rõ; Owner approve main | Toàn bộ notes có evidence |

## Chi tiết từng mốc

### M0 — Chốt nền tảng để bắt đầu code

- Làm rõ `developer` đã dùng để xuất bản và `develop` đang được rules quy định cho integration. Owner quyết tên/flow chính thức; không tự đổi authority hoặc tạo main release.
- Coordinator ghi operational branch và assignment; mỗi coder có branch/worktree riêng.
- Architect đề xuất module boundaries và ownership còn thiếu: server API, shared contracts, root build, CI, operations/docs. Owner chấp nhận mapping.
- Release dựng build/CI ở paths được giao; version Java/Spring/build tool/dependencies do Architect + Owner quyết khi thuộc thẩm quyền.
- Có hướng dẫn clone/setup/build/test thật và một pipeline kiểm tra tối thiểu chạy trên checkout sạch.

Exit: một Agent mới recover đúng role/branch; lệnh build/test hoạt động; paths cần cho M2 đều có owner.
Không cần triển khai toàn bộ tính năng ở mốc này.

### M1 — Thiết kế vừa đủ cho luồng job đầu tiên

- Chốt vocabulary: job, execution, attempt, worker và lifecycle nào thực sự cần. Không tạo tất cả entity từ danh sách mục tiêu.
- Định nghĩa valid transitions, invariants, identity/version, transaction boundaries, scheduling clock, error semantics.
- Chốt contract giữa Core ↔ Data ↔ SDK/worker, serialization và payload validation.
- Quyết định persistence/messaging cần thiết, ghi alternatives và trade-offs; không coi PostgreSQL/Kafka là đã được chọn chỉ vì workflow nhắc tới.
- Thiết kế failure scenarios trước code: hai schedulers, stale worker, commit thành công nhưng client mất response, retry sau lỗi, restart.
- Thống nhất threat model tối thiểu và performance workload mục tiêu; con số do Owner/Architect chốt, không ghi số đo khi chưa chạy.

Exit: design/ADR cần thiết được chấp nhận; Owner giải thích được WHY/WHAT/FLOW/FAILURE/TRADE-OFF; Coordinator có task M2 đủ acceptance.

### M2 — Một luồng chạy xuyên suốt

- Submit job hợp lệ, lưu durable state, worker nhận và thực thi, ghi outcome và query được trạng thái.
- SDK tối thiểu hoặc contract client đã duyệt phục vụ demo; không định nghĩa public API tùy tiện trong roadmap.
- Test payload lỗi, task chưa được đăng ký, handler failure, request lặp theo semantics đã chốt; giữ invariant khi dữ liệu chưa hợp lệ.
- Có correlation identifiers, logs không lộ payload nhạy cảm và metrics cơ bản ngay từ đầu.
- Có demo tái lập trên môi trường sạch và test infrastructure phù hợp với lựa chọn thiết kế.

Exit: happy path và failure path cơ bản có test evidence; Owner trace được một job qua code/transaction, không chỉ xem screenshot.

### M3 — Lease, heartbeat và recovery

- Claim atomic theo storage design; heartbeat/renew/expire/recover theo accepted owner/version semantics.
- Test scheduler cạnh tranh, worker crash, scheduler crash, process pause lâu, completion/heartbeat cũ đến sau recovery, restart và lost response.
- Phân biệt job được claim, handler đang chạy và side effect bên ngoài. Không suy từ một lease trong DB thành bảo đảm external side effect đúng một lần.
- Ghi rõ phạm vi chống trùng: state update nào được bảo vệ, execution nào có thể overlap, downstream cần idempotency/fencing/contract gì.

Exit: test chứng minh đúng invariant đã duyệt, gồm stale owner không overwrite state; không tuyên bố exactly-once khi chưa có evidence và phạm vi rõ.

### M4 — Retry, timeout, cancel và scheduling

- Delay/schedule time, priority và fairness theo phạm vi Owner chốt; không tự thêm cron/calendar API nếu chưa cần.
- Retry policy, attempt limit, backoff/jitter và history; phân biệt lỗi retryable/non-retryable.
- Timeout và cancellation phải mô tả được handler đang chạy, side effect đã phát sinh, late completion và resource cleanup.
- Test completion đồng thời với timeout/cancel, retry đến muộn, restart lúc chờ retry và giới hạn attempts.

Exit: outcomes/history nhất quán theo state machine; không retry vô hạn, không tạo worker/thread/executor leak.

### M5 — Durable messaging và xử lý duplicate

- Outbox cho transaction business state/event intent nếu design chọn pattern đó; publisher recovery và consumer ACK/offset theo contract.
- Event ID/schema/version, ordering scope, partition key, retention và tương thích do Architect + Data thiết kế.
- Idempotency scope/key/payload conflict/retention; phân biệt submit dedup, event dedup và side-effect idempotency.
- DLQ có lý do thất bại và quy trình replay/redrive được kiểm soát; test poison event không gây retry storm.
- Test crash trước/sau DB commit, trước/sau publish, trước/sau ACK, broker/DB outage và event trùng/out-of-order theo guarantee thực tế.

Exit: phân tích được từng crash window; duplicate handling và recovery có integration evidence; không gọi outbox là exactly-once.

### M6 — SDK/starter dùng được bên ngoài project

- App Java/Spring Boot mẫu tích hợp SDK/starter theo API được duyệt.
- Worker registration/discovery, startup/shutdown, reconnect, config validation, serialization và error mapping.
- Default retry/timeout/config cần rõ và an toàn trong phạm vi thiết kế; SDK không lộ Kafka/DB internals.
- Compatibility tests theo versions đã chọn; testing utilities có ví dụ thực tế.
- Test cấu hình lỗi, lifecycle lỗi, response mất/retry, unknown task/payload và serialization change.

Exit: người dùng làm theo quickstart chạy được; Owner chỉ ra được vì sao fluent API không che giấu failure semantics.

### M7 — Workflow/DAG

- Architect + Owner chốt workflow definition vs execution, DAG validation, dependency semantics, parallel tasks và propagation.
- Test chain, fan-out/fan-in, task failure, retry, cancellation, workflow restart và duplicate completion.
- Chống double scheduling của dependent task theo accepted invariant.
- Không mặc nhiên thêm saga/compensation DSL, UI designer hoặc dynamic workflow nếu chưa chốt scope.

Exit: workflow có durable progress/recovery; task con không vượt dependencies; Owner giải thích được partial failure bằng demo và code.

### M8 — Vận hành và security

- Metrics: queue age/depth, claims, retries, timeouts, DLQ, heartbeat/recovery, DB pool, messaging lag và latency/error rate phù hợp.
- Trace/log nối job/execution/attempt theo thiết kế; kiểm tra secret/payload redaction và metric cardinality.
- Authentication/authorization của interface exposed, input limits và threat model đã chốt; tenant isolation nếu multi-tenancy thuộc scope được duyệt.
- Runbook startup/shutdown/outage/backlog/DLQ/recovery; migration/rollback/upgrade có consideration.
- Không tự mở rộng sang SaaS billing, dashboard hay cloud platform mới.

Exit: tìm được nguyên nhân một job thất bại qua evidence; các security checks phù hợp có test; không có secret trong repo/log.

### M9 — Chaos và performance

- Failure matrix bám các scenarios của M3–M8; mỗi case có revision, môi trường, command và expected/actual outcome.
- Benchmark ghi hardware, versions, payload, workload, concurrency, warm-up, thời gian đo và error rate.
- Đo throughput, P50/P95/P99, resource/connection usage, lag/backlog; tách queue wait và handler duration.
- Chỉ tối ưu bottleneck đã đo; giữ correctness/regression tests khi đổi batching/index/executor.
- Owner chấp nhận workload/budget trước khi đánh giá đạt hay không; kết quả ghi đúng phạm vi, không tự hứa số job/giây.

Exit: failure suite pass; performance budget đã chốt đạt hoặc deviation được Owner quyết định; benchmark tái lập, trade-offs rõ.

### M10 — Hoàn thiện và release

- Quickstart, examples, design/ADR index, API docs, operation guide và known limitations nhất quán với code thật.
- Reviewer kiểm tra candidate cuối gồm exact code/test/dependency/base SHAs; không kế thừa approval cho HEAD đã đổi.
- Release validate integration result, ghi release scope/version/changes/risks và prepare main release.
- Owner walkthrough demo, failure demo và explanation notes; main chỉ merge khi Owner approve release cụ thể.
- Quyền publish package/artifact hoặc deploy được xác nhận riêng; không suy từ quyền merge.

Exit: candidate có evidence đầy đủ, Owner hiểu các trade-offs chính, release được thực hiện đúng quyền.

## Gate dùng chung cho mỗi mốc

Theo [.ai/workflows/feature.md](.ai/workflows/feature.md): Design → Implement → Test → Review → Fix → Approve → Integrate.
Milestone không COMPLETE vì code đã commit. Phải có acceptance evidence, mandatory tests pass, Reviewer approval đúng revision và Release integration theo rule đang hiệu lực.
Không có unresolved CRITICAL/HIGH; MEDIUM xử lý theo rule đã duyệt.
Roadmap không nhân bản task state: dùng task board/review/release làm nguồn thực tế.

Checkpoint học code: Owner có thể chỉ ra code/symbol tại revision, giải thích invariant, một failure/race, alternative và test chứng minh. Notes không phải thêm một approval gate mới.

## Ba bước tiếp theo được đề xuất

1. Coordinator/Release đưa vấn đề developer/develop và operational branch vào quyết định Owner trước normal integration.
2. Architect đề xuất design nhỏ cho submit → persist → execute → outcome, cùng boundaries và path owners cần thiết.
3. Coordinator tách design đã được chấp nhận thành một task xuyên suốt M2; SDK/Core/Data làm theo contracts; Reliability chuẩn bị failure tests.

Đây là thứ tự đề xuất; không tự khởi chạy Agent, tạo assignment hoặc triển khai feature.
