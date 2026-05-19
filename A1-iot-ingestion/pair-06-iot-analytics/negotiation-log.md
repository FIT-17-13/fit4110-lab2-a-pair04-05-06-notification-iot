# Negotiation Log - Pair 06 (IoT Ingestion -> Analytics)

- Pair: IoT Ingestion -> Analytics (pair-06, Queue async)
- Product: A
- Provider: A1 IoT Ingestion Service
- Consumer: A5 Analytics Service
- Version: v1.0
- Date: 2026-05-19

---

## Issue #1

- Raised by: Consumer
- Topic/Event: telemetry.ingested
- Concern: Aggregate key unclear (deviceId vs zoneId)
- Proposal: deviceId required, zoneId optional but recommended
- Resolution: Accepted
- Rationale: Backward compatible for devices without zone mapping
- Impact: Provider includes zoneId when available

---

## Issue #2

- Raised by: Provider
- Topic/Event: telemetry.ingested
- Concern: Unit mapping for metrics
- Proposal: Provide unit map in payload (temperature, humidity, motion)
- Resolution: Accepted
- Rationale: Analytics uses units for normalization
- Impact: Add unit object in data

---

## Issue #3

- Raised by: Consumer
- Topic/Event: telemetry.ingested
- Concern: Batching vs single event
- Proposal: Single event in Lab 02, allow batch in Lab 03
- Resolution: Accepted
- Rationale: Keep contract simple now
- Impact: Update in Lab 03 if needed

---

## Issue #4

- Raised by: Provider
- Topic/Event: device.status.changed
- Concern: Status values not defined
- Proposal: Enum: online, offline, degraded
- Resolution: Accepted
- Rationale: Normalize analytics KPI
- Impact: Consumer handles defined enum

---

## Issue #5

- Raised by: Consumer
- Topic/Event: all
- Concern: Duplicate events on retry
- Proposal: Use eventId as idempotency key
- Resolution: Accepted
- Rationale: Avoid double-counting
- Impact: Consumer deduplicates by eventId

---

## Issue #6

- Raised by: Provider
- Topic/Event: retry/DLQ
- Concern: Undefined retry policy
- Proposal: Broker retries 3 times then DLQ
- Resolution: Accepted
- Rationale: Prevent infinite retry loops
- Impact: Consumer monitors DLQ metrics

---

# Sign-off v1.0

Provider sign-off: Nguyen Viet Quang  
Consumer sign-off:  
Witness (GV/TA):    
Date: 2026-05-19
