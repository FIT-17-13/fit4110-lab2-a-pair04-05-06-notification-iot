# Requirement Analysis - Provider

- Pair: IoT Ingestion -> Analytics (pair-06, Queue async)
- Product: A
- Provider service: A1 IoT Ingestion Service
- Consumer service: A5 Analytics Service
- Author: Nguyen Viet Quang
- Date: 2026-05-19

---

## 1. Core resources

| Resource | Description | Required fields | Optional fields |
|---|---|---|---|
| TelemetryIngestedEvent | Telemetry feed for aggregation | eventId, eventType, occurredAt, correlationId, deviceId, metrics, capturedAt | zoneId, locationId, tags |
| DeviceStatusChangedEvent | Device status update | eventId, eventType, occurredAt, correlationId, deviceId, status, updatedAt | reason |

---

## 2. Expected actions

| Action | Topic/Event | When emitted? |
|---|---|---|
| Publish telemetry | iot.telemetry.ingested / telemetry.ingested | For each telemetry batch or reading |
| Publish device status | iot.device.status / device.status.changed | When device goes online/offline |

---

## 3. Error cases

| Case | Handling |
|---|---|
| Invalid payload | Drop or DLQ |
| Duplicate eventId | Ignore (idempotent) |
| Missing metrics | Reject and log |
| Broker unavailable | Retry with backoff |
| Consumer failure | Consumer handles retries |

---

## 4. Additional assumptions

- Metrics use consistent units (metric system).
- Analytics aggregates by deviceId and time window.
- All timestamps are UTC.

---

## 5. Questions for Consumer

1. Do you need zoneId for aggregation?
2. Is batching required in Lab 03?
3. What is the acceptable delay for telemetry?

---

## 6. Integration risks

| Risk | Impact | Mitigation |
|---|---|---|
| Missing zoneId | Wrong aggregation | Make zoneId optional but recommended |
| High throughput | Queue lag | Agree sampling interval and batching |
