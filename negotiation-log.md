# Biên bản đàm phán hợp đồng API

- Cặp đàm phán: Core Business → Notification (pair-04, Queue async)
- Product: 
- Provider: A7 Notification Service
- Consumer: A6 Core Business Service
- Phiên: v1.0
- Ngày: 2026-05-18

---

## Issue #1

- Raised by: Consumer
- Endpoint: Event `alert.created`
- Concern: Event name chưa thống nhất giữa các team
- Proposal: Dùng prefix `alert.*` cho notification pipeline
- Resolution: Accepted
- Rationale: Dễ mở rộng cho `alert.escalated`, `alert.resolved`
- Impact: Core Business và Notification thống nhất naming

---

## Issue #2

- Raised by: Provider
- Endpoint: Event payload
- Concern: Thiếu định danh và correlation id
- Proposal: Bắt buộc `alertId`, `eventId`, `correlationId`, `timestamp`
- Resolution: Accepted
- Rationale: Trace end-to-end, tránh duplicate
- Impact: Consumer phải bổ sung các field này

---

## Issue #3

- Raised by: Consumer
- Endpoint: Event payload
- Concern: Có cần `requestedChannels` hay Provider tự định tuyến
- Proposal: Field `requestedChannels` là optional; Provider tự chọn nếu thiếu
- Resolution: Accepted
- Rationale: Duy trì flexibility cho Core Business
- Impact: Provider cần rule engine mặc định

---

## Issue #4

- Raised by: Provider
- Endpoint: Idempotency
- Concern: Retry từ queue gây gửi trùng
- Proposal: Dùng `alertId` làm idempotency key; ignore duplicate trong 24h
- Resolution: Accepted
- Rationale: Giảm duplicate, phù hợp workload
- Impact: Provider cần dedup cache

---

## Issue #5

- Raised by: Consumer
- Endpoint: Retry assumption
- Concern: Ai chịu trách nhiệm retry
- Proposal: Queue retry 2 lần; Provider retry nội bộ thêm 3 lần
- Resolution: Modified
- Rationale: Tránh duplicate quá nhiều; thống nhất retry tối đa 3 lần tại Provider
- Impact: Queue chỉ retry 1 lần, Provider retry 3 lần

---

## Issue #6

- Raised by: Provider
- Endpoint: Delivery status
- Concern: Consumer có cần callback trạng thái gửi không
- Proposal: Provider expose REST `GET /notifications/{id}` + optional event `notification.delivery.status`
- Resolution: Accepted
- Rationale: Đủ cho audit, không bắt buộc callback
- Impact: Consumer chủ động pull status khi cần

---

# Chốt hợp đồng v1.0

Provider sign-off: Nguyễn Việt Quang  
Consumer sign-off: (đại diện Core Business)  
Witness (GV/TA):    
Date: 2026-05-15

---

## Ghi chú warning nếu Spectral còn cảnh báo

| Warning | Lý do chấp nhận tạm thời | Kế hoạch sửa |
|---|---|---|
|  |  |  |
