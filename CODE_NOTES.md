# Chronos — Sổ đọc code, giải thích và phỏng vấn

Mục đích: ghi những đoạn code cần hiểu kỹ và chứng cứ giúp giải thích quyết định kỹ thuật.
Khởi tạo theo yêu cầu Owner ngày 2026-10-03. Tham chiếu [ROADMAP.md](ROADMAP.md).
Hiện chưa có application code. Các mục CN bên dưới là danh sách cần đọc khi code xuất hiện, không phải review findings hay kết luận implementation.

Owner có thể ghi chú trực tiếp. Agent chỉ sửa notebook khi được Owner giao rõ; nếu chưa có quyền, đưa đề xuất/code hotspots trong status hoặc handoff của mình để Owner đưa vào đây.
Tài liệu này không thay ADR, review hay task board. Nhận xét design cần quyết định vẫn đi qua Architect + Owner.

## Cách ghi để hữu ích sau vài tháng

- Một entry cho một quyết định hoặc đoạn logic khó; không note mọi getter, DTO hoặc tên class.
- Gắn task/ADR/review, commit SHA, path và symbol. Line number chỉ là tiện ích; symbol + SHA giúp tìm lại khi code thay đổi.
- Dẫn test chứng minh và kết quả trên revision đã test; không ghi “thread-safe”, “exactly-once” hay “scale tốt” chỉ vì đọc thấy annotation/pattern.
- Tách code thực tế, guarantee có evidence, hạn chế/assumption và câu hỏi chưa giải quyết.
- Sau refactor, cập nhật mapping và đánh dấu note cũ STALE cho tới khi đọc lại.
- Trong giải thích/phỏng vấn, nói rõ phần bản thân thiết kế, phần AI hỗ trợ và cách mình kiểm chứng; không nhận công chưa thực hiện.

Mức ghi chú cá nhân: TODO (chưa đọc code), READING, EXPLAINED (tự giải thích được với evidence), STALE.
Đây không phải trạng thái task/review. “EXPLAINED” không cấp quyền merge.

## Danh mục ưu tiên

| ID | Chủ đề cần đọc kỹ | Mốc | Ưu tiên | Code / trạng thái | Câu hỏi phỏng vấn trọng tâm |
|---|---|---|---|---|---|
| CN-01 | Module boundaries, dependency direction, build/CI | M0–M1 | P1 | Chưa có code; TODO | Vì sao tách module như vậy? Tránh server internals rò sang SDK thế nào? |
| CN-02 | Job lifecycle, execution/attempt và domain invariants | M1–M2 | P0 | Chưa có code; TODO | Vì sao state transition nằm trong domain? Job khác attempt ở đâu? |
| CN-03 | Transaction boundary và atomic job claim | M1–M3 | P0 | Chưa có code; TODO | Vì sao SELECT rồi UPDATE riêng có thể race? Lock sống bao lâu? |
| CN-04 | Lease/heartbeat, stale worker và recovery | M3 | P0 | Chưa có code; TODO | Worker A sống lại sau khi B nhận job thì sao? State và side effect được bảo vệ khác nhau thế nào? |
| CN-05 | Retry/backoff/jitter, timeout/cancel races | M4 | P0 | Chưa có code; TODO | Timeout có dừng side effect không? Completion đến sau cancel xử lý ra sao? |
| CN-06 | Idempotency: submit/event/business effect | M2–M5 | P0 | Chưa có code; TODO | Cùng key nhưng khác payload? Dedup request có ngăn email gửi hai lần không? |
| CN-07 | Transactional outbox và crash windows | M5 | P0 | Chưa có code; TODO | DB commit xong nhưng chưa publish thì sao? Publish xong nhưng chưa đánh dấu thì sao? |
| CN-08 | Kafka ordering/ACK/offset, DLQ và replay | M5 | P0 | Chưa có code; TODO | Commit offset trước hay sau processing? Guarantee của Kafka áp dụng tới đâu? |
| CN-09 | SDK public contract, Spring lifecycle, serialization | M2–M6 | P1 | Chưa có code; TODO | Auto-configuration hoạt động thế nào? Proxy/self-invocation ảnh hưởng transaction ra sao? |
| CN-10 | DAG dependency, fan-in và durable workflow state | M7 | P0 | Chưa có code; TODO | Hai completion đồng thời có schedule task con hai lần không? Restart giữa fan-in thì sao? |
| CN-11 | Observability, security và graceful shutdown | M2–M8 | P1 | Chưa có code; TODO | Trace một job bằng gì? Payload nào không được log? Shutdown khi handler đang chạy thì sao? |
| CN-12 | Failure tests, query plans và performance evidence | M3–M9 | P1 | Chưa có code; TODO | Chứng minh race bằng test nào? P99 đo latency nào, trên workload gì? |

P0: dễ làm sai distributed correctness hoặc là điểm cần giải thích sâu.
P1: hỗ trợ khả năng tích hợp, vận hành và đánh giá kỹ thuật.
Không có file/symbol/link code giả trong danh mục. Chỉ điền khi implementation thực sự tồn tại.

## Mẫu entry để copy ngay trong file này

### CN-XX — Tên quyết định/đoạn code

- Trạng thái học: TODO / READING / EXPLAINED / STALE.
- Mốc / task: ...
- Ngày đọc / người ghi: ...
- Commit code đã đọc: ...
- File / symbol / line tham khảo: ...
- Permalink tới code tại SHA: ...
- Design/ADR; review finding liên quan: ...
- Code owner: ... (theo assignment, không đoán từ người commit).

**Bài toán:** đoạn code giải quyết tình huống nào? Nếu bỏ nó, failure nào xảy ra?

**Luồng thực tế:** input → validation → state/transaction → external call/event → outcome.
Ghi transaction bắt đầu/kết thúc ở đâu, locks/resources sống bao lâu; không chỉ kể tên method.

**Invariant / guarantee và giới hạn:** điều luôn cần đúng; điều code bảo vệ được; assumption môi trường/downstream; điều chưa được bảo đảm.

**Race / crash timeline:** T0 ...; T1 ...; T2 ...; interleaving nào gây lỗi; code nào chặn nó?

**Vì sao chọn cách này:** alternatives đã xem xét; trade-off correctness/latency/throughput/complexity/compatibility; decision reference.

**Test chứng minh:** test name/path, command, environment, tested SHA, expected/actual result. Thiếu evidence ghi UNVERIFIED.

**Code cần chú ý kỹ:** điều kiện SQL/CAS, annotation/proxy, mutable state, token/version, ACK, cleanup, executor hoặc serialization; mô tả vì sao từng dòng quan trọng.

**Giải thích 60 giây:** vấn đề → cách giải quyết → một failure case → test → giới hạn. Viết bằng lời của mình.

**Câu hỏi đào sâu:** 2–3 câu “nếu ... thì sao?” và câu trả lời dựa trên code hiện tại.

**Chưa hiểu / cần hỏi:** ...; gửi Architect/code owner nếu ảnh hưởng correctness.

Không cần điền hết các mục cho một note nhỏ; ưu tiên source revision, invariant, failure và test.
Không dán nhiều trang code: dùng permalink tới code và trích đoạn nhỏ khi cần.

## Hai entry khởi đầu — chưa có implementation

### CN-04 — Worker cũ quay lại sau khi lease đã được phục hồi

Trạng thái: TODO. Code/commit/test: chưa có. Đây là tình huống cần thiết kế và kiểm chứng, không phải cách Chronos đã triển khai.

Timeline cần mô phỏng:

1. Worker A nhận ownership hợp lệ.
2. A bị pause hoặc mất liên lạc; lease hết hạn theo design.
3. Worker B được cấp ownership mới.
4. A chạy tiếp và gửi heartbeat/completion cũ.
5. Kiểm tra state update của A có bị từ chối và side effect bên ngoài có thể xảy ra lại không.

Khi có code, tìm chính điều kiện owner/version/token trong claim, renew, complete và recover; không chỉ tìm một biến tên lease.
Nếu design chọn fencing/version check, phải chỉ ra nơi **kiểm tra** token. Token được sinh ra nhưng không được downstream kiểm tra không tự bảo vệ side effect; đây là câu hỏi review cần chứng minh trên contract thực tế.
Cần phân biệt “stale result không được chấp nhận” với “handler không bao giờ chạy trùng”. Không điền guarantee thứ hai khi chỉ có test của state update.

Test cần tìm: stale heartbeat/completion, hai schedulers, worker pause/resume, recovery/restart. Đây là mục tiêu test, chưa phải tên test đã tồn tại.
Câu hỏi: “Tại sao distributed lease không đủ để tự khẳng định mọi external effect chỉ xảy ra một lần?”

Phần giải thích 60 giây của tôi: _điền sau khi đọc code và test thật_.

### CN-07 — DB commit và publish event không hoàn tất cùng lúc

Trạng thái: TODO. Code/commit/test: chưa có. Outbox là chủ đề trong mục tiêu; design cụ thể vẫn cần Architect + Owner.

Các crash window cần đánh dấu trong code khi pattern được chọn:

| Điểm crash | Câu hỏi cần chứng minh |
|---|---|
| Trước commit business state + event intent | Có rollback cả hai không? |
| Sau commit nhưng trước publish | Publisher tìm lại event thế nào? |
| Sau broker nhận nhưng trước ghi nhận publish/ACK | Duplicate được nhận diện và xử lý ở đâu? |
| Sau consumer side effect nhưng trước ACK/offset commit | Retry/re-delivery có lặp side effect không? |

Tìm transaction atomic thực tế, event identity, publisher retry/checkpoint, consumer dedup và downstream idempotency contract.
Không gọi outbox là exactly-once chỉ vì không mất event intent. Kafka cũng mô tả rằng exactly-once với đích ngoài Kafka cần phối hợp với hệ thống đích; áp dụng cụ thể tới Chronos vẫn phải được design và test. [Apache Kafka — Message Delivery Semantics](https://kafka.apache.org/40/design/design/#message-delivery-semantics).

Câu hỏi: “Nếu database và broker không commit chung một transaction, bạn giải thích khoảng hở và recovery bằng code nào?”
Phần giải thích 60 giây của tôi: _điền sau khi đọc code và test thật_.

## Luyện giải thích/phỏng vấn theo dự án thật

| Bài tập | Evidence để chuẩn bị |
|---|---|
| 2 phút: Chronos giải quyết gì và ai dùng? | Phạm vi thật, SDK/demo và giới hạn phiên bản |
| 5 phút: trace một job từ submit đến outcome | Sequence trên code hiện tại, transaction và metrics |
| 10 phút: scheduler/worker chết giữa chừng | Một timeline failure và test/reproduction đã chạy |
| 10 phút: review race hoặc duplicate side effect | Code tại SHA, invariant, interleaving và regression test |
| 5 phút: tại sao lựa chọn thiết kế này? | ADR, alternative, trade-off và phạm vi guarantee |
| 5 phút: hiệu năng được chứng minh thế nào? | Workload/hardware, raw result, latency definition, trước/sau |
| 5 phút: bản thân đã đóng góp gì? | Task/commit/decision và phần AI hỗ trợ, cách Owner kiểm chứng |

Câu hỏi dùng để kiểm tra hiểu code; không học thuộc một câu trả lời mẫu chưa đúng với implementation.

## Tài liệu gốc để kiểm tra khái niệm

Các link phục vụ học và đối chiếu, không chọn dependency version cho Chronos:

- Nếu dùng PostgreSQL `SKIP LOCKED`: đọc locking clause. Nó bỏ qua row chưa lock được và không cung cấp một view đầy đủ/nhất quán cho truy vấn thông thường; cần kiểm tra transaction và ordering trên code thực tế. [PostgreSQL — SELECT, locking clause](https://www.postgresql.org/docs/current/sql-select.html#SQL-FOR-UPDATE-SHARE).
- Kafka: phân biệt delivery, processing, offset và external side effects trước khi nói exactly-once. [Apache Kafka — Message Delivery Semantics](https://kafka.apache.org/40/design/design/#message-delivery-semantics).

Checked on 2026-10-03. Khi project chốt versions, đối chiếu tài liệu đúng version thay vì suy từ link current.
