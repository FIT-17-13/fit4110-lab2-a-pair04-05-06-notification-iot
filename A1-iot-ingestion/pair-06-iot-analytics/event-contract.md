# Event Contract - Pair 06 (IoT Ingestion -> Analytics)

## 1. Dependency info

- Dependency no: 6
- Producer: A1 IoT Ingestion Service
- Consumer: A5 Analytics Service
- Mechanism: Queue async
- Author: Nguyen Viet Quang
- Date: 2026-05-19

## 2. Business purpose

IoT Ingestion feeds telemetry to Analytics for aggregation and dashboards.

## 3. Event name / topic

| Item | Value |
|---|---|
| Event name | telemetry.ingested, device.status.changed |
| Topic/queue | iot.telemetry.ingested, iot.device.status |
| Producer | A1 IoT Ingestion |
| Consumer | A5 Analytics |

## 4. Minimal payload

```json
{
  "eventId": "uuid",
  "eventType": "telemetry.ingested",
  "occurredAt": "2026-05-19T09:45:00Z",
  "correlationId": "uuid",
  "source": "iot-ingestion",
  "data": {
    "deviceId": "esp32-a101",
    "metrics": {
      "temperature": 31.5,
      "humidity": 72,
      "motion": 1
    },
    "unit": {
      "temperature": "celsius",
      "humidity": "percent",
      "motion": "boolean"
    },
    "capturedAt": "2026-05-19T09:44:50Z",
    "zoneId": "zone-a"
  }
}
```

For device status:

```json
{
  "eventId": "uuid",
  "eventType": "device.status.changed",
  "occurredAt": "2026-05-19T09:45:10Z",
  "correlationId": "uuid",
  "source": "iot-ingestion",
  "data": {
    "deviceId": "esp32-a101",
    "status": "online",
    "updatedAt": "2026-05-19T09:45:00Z",
    "reason": "heartbeat"
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

1. AsyncAPI channel definitions and schema evolution.
2. Batch telemetry and compression strategy.
3. Aggregate key (deviceId vs zoneId).
