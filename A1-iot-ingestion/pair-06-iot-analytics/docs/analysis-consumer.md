# Requirement Analysis - Consumer

- Pair: IoT Ingestion -> Analytics (pair-06, Queue async)
- Product: A
- Consumer service: A5 Analytics Service
- Provider service: A1 IoT Ingestion Service
- Author: Nguyen Viet Quang
- Date: 2026-05-19

---

## 1. Resources Consumer needs

| Resource | Consumer use | Required fields | Optional fields |
|---|---|---|---|
| TelemetryIngestedEvent | Aggregate metrics by time window | eventId, deviceId, metrics, capturedAt | zoneId, locationId, tags |
| DeviceStatusChangedEvent | Update device availability KPI | eventId, deviceId, status, updatedAt | reason |

---

## 2. Events to consume

| Event | When used? | Expected behavior |
|---|---|---|
| telemetry.ingested | Continuous aggregation | Update dashboards and KPIs |
| device.status.changed | Device availability | Update uptime metrics |

---

## 3. Error handling needs

| Case | Consumer handling |
|---|---|
| Missing metrics | Drop and log |
| Duplicate eventId | Ignore (idempotent) |
| Out-of-order timestamps | Use capturedAt for window |
| Invalid status value | Map to UNKNOWN |
| Broker retry | Ensure idempotency |

---

## 4. Additional assumptions

- Analytics aggregates by deviceId and hourly windows.
- correlationId is preferred for traceability.
- Device status values: online, offline, degraded.

---

## 5. Questions for Provider

1. Can provider send telemetry in batch?
2. How often do device status events emit?
3. Is zoneId always available?

---

## 6. Integration risks

| Risk | Impact | Mitigation |
|---|---|---|
| Schema drift | Analytics parsing error | Fix event contract and versioning |
| High volume | Missed windows | Throttle or batch in Lab 03 |
