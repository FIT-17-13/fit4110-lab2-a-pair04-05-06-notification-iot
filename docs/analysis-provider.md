# Phân tích yêu cầu — vai Provider

- Cặp đàm phán: Core Business → Notification (pair-04, Queue async)
- Product: A
- Provider service: A7 Notification Service
- Consumer service: A6 Core Business Service
- Người viết: Nguyễn Việt Quang
- Ngày: 2026-05-15

---

## 1. Resource chính

| Resource | Mô tả | Thuộc tính bắt buộc | Thuộc tính tùy chọn |
|---|---|---|---|
| NotificationRequest | Yêu cầu gửi thông báo từ Core Business | alertId, severity, message, target | templateId, variables, requestedChannels |
| NotificationStatus | Trạng thái xử lý và kết quả gửi | notificationId, alertId, status, queuedAt | deliveredAt, delivery[] |

---

## 2. Action/API dự kiến

| Method | Path | Mục đích | Consumer gọi khi nào? |
|---|---|---|---|
| POST | `/notifications` | Enqueue thông báo để gửi | Khi Core Business phát hiện alert cần gửi |
| GET | `/notifications/{notificationId}` | Tra cứu trạng thái gửi | Khi cần kiểm tra delivery status |
| GET | `/notifications` | Liệt kê thông báo gần đây | Khi cần audit hoặc dashboard |
| POST | `/channels/test` | Test kênh gửi | Khi admin kiểm tra cấu hình channel |

---

## 3. Error case

Tối thiểu 5 case.

| Status | Tình huống | Response body dự kiến |
|---:|---|---|
| 400 | Payload sai định dạng (thiếu alertId, message rỗng) | `ProblemDetails` |
| 401 | Thiếu Bearer token | `ProblemDetails` |
| 403 | Token hợp lệ nhưng không có quyền | `ProblemDetails` |
| 404 | Không tìm thấy notificationId | `ProblemDetails` |
| 409 | Duplicate alertId (idempotency) | `ProblemDetails` |
| 422 | Severity không hợp lệ | `ProblemDetails` |

---

## 4. Giả định bổ sung

Ghi rõ những điểm user story chưa nói nhưng Provider cần giả định.

- Notification tự định tuyến channel theo severity nếu Consumer không chỉ định.
- alertId là idempotency key để tránh gửi trùng.
- Retry tối đa 3 lần; nếu thất bại sẽ ghi log và đưa vào dead-letter.

---

## 5. Câu hỏi cho Consumer

1. Consumer có cần gửi `requestedChannels` hay để Provider tự chọn?
2. `target` định danh theo team/user hay cần hỗ trợ webhook?
3. Consumer có cần callback/event báo trạng thái gửi không?

---

## 6. Rủi ro tích hợp

| Rủi ro | Tác động | Đề xuất xử lý |
|---|---|---|
| Tên field không thống nhất | Consumer parse lỗi | Chốt naming trong `openapi.yaml` |
| Payload quá lớn | Timeout/mock lỗi | Giới hạn message, variables size |
| Retry gây duplicate | Gửi trùng thông báo | Dùng alertId làm idempotency key |
