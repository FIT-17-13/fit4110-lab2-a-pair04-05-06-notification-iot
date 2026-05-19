# Requirement Analysis - Consumer

- Pair: IoT Ingestion -> Core Business (pair-05, Queue async)
- Product: A
- Consumer service: A6 Core Business Service
- Provider service: A1 IoT Ingestion Service
- Author: Nguyen Viet Quang
- Date: 2026-05-19

---

## 1. Resources Consumer needs

| Resource | Consumer use | Required fields | Optional fields |
|---|---|---|---|
| SensorReadingEvent | Evaluate policy and detect anomaly | eventId, deviceId, sensorType, value, unit, capturedAt | locationId, metadata |
| SensorThresholdEvent | Trigger alert and escalation | eventId, deviceId, sensorType, value, threshold, capturedAt | policyId, locationId |

---

## 2. Events to consume

| Event | When used? | Expected behavior |
|---|---|---|
| sensor.reading.created | Continuous monitoring | Update state, run rules |
| sensor.threshold.exceeded | Immediate alert | Create alert and notify |

---

## 3. Error handling needs

| Case | Consumer handling |
|---|---|
| Missing deviceId | Ignore and log |
| Invalid unit | Map to default or drop |
| Duplicate eventId | Skip (idempotent) |
| Out-of-order timestamps | Use capturedAt for ordering |
| Payload too large | Reject and log |

---

## 4. Additional assumptions

- correlationId is optional for analytics but preferred for tracing.
- Core Business expects UTC timestamps.
- Threshold events include the threshold value.

---

## 5. Questions for Provider

1. Is locationId always available?
2. Can provider send batch events in the future?
3. What is the retry policy when broker is down?

---

## 6. Integration risks

| Risk | Impact | Mitigation |
|---|---|---|
| Field naming inconsistency | Parser failure | Lock schema in event contract |
| Duplicate events | Multiple alerts | Use eventId as idempotency key |
