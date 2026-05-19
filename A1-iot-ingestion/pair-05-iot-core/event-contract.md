# Event Contract - Pair 05 (IoT Ingestion -> Core Business)

## 1. Dependency info

- Dependency no: 5
- Producer: A1 IoT Ingestion Service
- Consumer: A6 Core Business Service
- Mechanism: Queue async
- Author: Nguyen Viet Quang
- Date: 2026-05-19

## 2. Business purpose

IoT Ingestion publishes sensor readings so Core Business can evaluate policies and detect anomalies.

## 3. Event name / topic

| Item | Value |
|---|---|
| Event name | sensor.reading.created, sensor.threshold.exceeded |
| Topic/queue | iot.sensor.reading, iot.sensor.threshold |
| Producer | A1 IoT Ingestion |
| Consumer | A6 Core Business |

## 4. Minimal payload

```json
{
  "eventId": "uuid",
  "eventType": "sensor.reading.created",
  "occurredAt": "2026-05-19T09:30:00Z",
  "correlationId": "uuid",
  "source": "iot-ingestion",
  "data": {
    "deviceId": "esp32-a101",
    "sensorType": "temperature",
    "value": 31.5,
    "unit": "celsius",
    "capturedAt": "2026-05-19T09:29:58Z",
    "locationId": "gate-01"
  }
}
```

For threshold exceeded:

```json
{
  "eventId": "uuid",
  "eventType": "sensor.threshold.exceeded",
  "occurredAt": "2026-05-19T09:30:05Z",
  "correlationId": "uuid",
  "source": "iot-ingestion",
  "data": {
    "deviceId": "esp32-a101",
    "sensorType": "temperature",
    "value": 42.0,
    "unit": "celsius",
    "threshold": 40.0,
    "capturedAt": "2026-05-19T09:30:00Z",
    "locationId": "gate-01"
  }
}
```

## 5. Constraints

| Issue | Decision |
|---|---|
| eventId required | Yes, UUID | 
| correlationId required | Yes, UUID | 
| idempotency | Use eventId as idempotency key | 
| retry | Broker retry 3 times, then DLQ | 
| allowed duplicate | Possible, consumer must be idempotent | 

## 6. Items for Lab 03

1. AsyncAPI channel definitions and schema versioning.
2. Partition key strategy (deviceId vs locationId).
3. Retention and replay policy.
