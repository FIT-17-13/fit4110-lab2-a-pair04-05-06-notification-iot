# Requirement Analysis - Provider

- Pair: IoT Ingestion -> Core Business (pair-05, Queue async)
- Product: A
- Provider service: A1 IoT Ingestion Service
- Consumer service: A6 Core Business Service
- Author: Nguyen Viet Quang
- Date: 2026-05-19

---

## 1. Core resources

| Resource | Description | Required fields | Optional fields |
|---|---|---|---|
| SensorReadingEvent | Raw sensor reading published by IoT Ingestion | eventId, eventType, occurredAt, correlationId, deviceId, sensorType, value, unit, capturedAt | locationId, metadata |
| SensorThresholdEvent | Threshold exceeded event | eventId, eventType, occurredAt, correlationId, deviceId, sensorType, value, unit, threshold, capturedAt | locationId, policyId |

---

## 2. Expected actions

| Action | Topic/Event | When emitted? |
|---|---|---|
| Publish sensor reading | iot.sensor.reading / sensor.reading.created | Every valid reading from device |
| Publish threshold exceeded | iot.sensor.threshold / sensor.threshold.exceeded | When value crosses configured threshold |

---

## 3. Error cases

| Case | Handling |
|---|---|
| Invalid payload (missing deviceId) | Drop or send to DLQ |
| Invalid unit or sensorType | Reject and log |
| Duplicate eventId | Ignore (idempotent) |
| Broker unavailable | Retry with backoff |
| Consumer processing failure | Consumer handles, Provider keeps retry policy |

---

## 4. Additional assumptions

- Timestamp fields are ISO-8601 in UTC.
- Units follow metric system (Celsius, percent, ppm).
- eventId is a UUID string and is unique.

---

## 5. Questions for Consumer

1. Does Core Business require locationId for every reading?
2. Should motion events be encoded as sensorType=motion with value 0/1?
3. Max acceptable delay for processing events?

---

## 6. Integration risks

| Risk | Impact | Mitigation |
|---|---|---|
| Value type mismatch | Consumer parse error | Fix schema and examples in event contract |
| Large payload volume | Queue backlog | Agree rate limits and batching in Lab 03 |
