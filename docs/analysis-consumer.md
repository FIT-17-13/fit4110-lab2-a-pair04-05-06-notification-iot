# Phân tích yêu cầu — vai Consumer

- Cặp đàm phán: Core Business → Notification (pair-04, Queue async)
- Product: A
- Consumer service: A6 Core Business Service
- Provider service: A7 Notification Service
- Người viết: Nguyễn Việt Quang
- Ngày: 2026-05-15

---

## 1. Resource Consumer cần nhận/gửi

| Resource | Consumer dùng để làm gì? | Field bắt buộc với Consumer | Field có thể tùy chọn |
|---|---|---|---|
| NotificationRequest | Gửi alert để Notification xử lý | alertId, severity, message, target | templateId, variables, requestedChannels |
| NotificationStatus | Theo dõi trạng thái gửi | notificationId, status, queuedAt | deliveredAt, delivery[] |

---

## 2. API Consumer cần gọi

| Method | Path | Lúc nào gọi? | Kỳ vọng response |
|---|---|---|---|
| POST | `/notifications` | Khi phát hiện alert cần gửi | 202 + notificationId |
| GET | `/notifications/{notificationId}` | Khi cần kiểm tra trạng thái | 200 + status/delivery |
| GET | `/notifications` | Khi cần dashboard/audit | 200 + danh sách |

---

## 3. Error case Consumer cần xử lý

Tối thiểu 5 case.

| Status | Consumer hiểu là gì? | Consumer sẽ xử lý thế nào? |
|---:|---|---|
| 400 | Payload sai schema | Sửa payload/log lỗi |
| 401 | Thiếu token | Refresh/cấu hình token |
| 403 | Không đủ quyền | Báo lỗi quyền truy cập |
| 404 | Không tìm thấy notificationId | Hiển thị trạng thái không tồn tại |
| 409 | Duplicate alertId | Dùng lại notificationId cũ |
| 422 | Severity không hợp lệ | Mapping lại rule severity |

---

## 4. Giả định bổ sung

- Notification sẽ tự định tuyến channel nếu Consumer không chỉ định.
- alertId là duy nhất trên từng alert.
- Delivery status có thể truy vấn sau (event callback là optional).

---

## 5. Câu hỏi cho Provider

1. Provider có yêu cầu field `templateId` bắt buộc không?
2. Provider có trả về `delivery` chi tiết cho từng channel không?
3. Khi queue backlog, Provider có trả 202 nhưng xử lý trễ bao lâu?

---

## 6. Rủi ro tích hợp

| Rủi ro | Tác động | Đề xuất xử lý |
|---|---|---|
| Provider đổi kiểu dữ liệu | Consumer parse lỗi | Chốt type/format/pattern |
| Provider thiếu mã lỗi | Consumer khó xử lý lỗi | Chuẩn hóa Problem Details |
| Retry gây duplicate | Gửi trùng thông báo | Dùng alertId làm idempotency key |
