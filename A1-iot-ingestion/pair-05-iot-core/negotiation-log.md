# Negotiation Log - Pair 05 (IoT Ingestion -> Core Business)

- Pair: IoT Ingestion -> Core Business (pair-05, Queue async)
- Product: A
- Provider: A1 IoT Ingestion Service
- Consumer: A6 Core Business Service
- Version: v1.0
- Date: 2026-05-19

---

## Issue #1

- Raised by: Consumer
- Topic/Event: sensor.reading.created
- Concern: Units and timezone are not explicit
- Proposal: Use metric units and ISO-8601 UTC timestamps
- Resolution: Accepted
- Rationale: Consistent parsing and policy checks
- Impact: Provider emits unit + UTC timestamps

---

## Issue #2

- Raised by: Provider
- Topic/Event: sensor.threshold.exceeded
- Concern: Need threshold value in payload
- Proposal: Add field data.threshold
- Resolution: Accepted
- Rationale: Core Business needs threshold for auditing
- Impact: Provider includes threshold in event

---

## Issue #3

- Raised by: Consumer
- Topic/Event: sensor.reading.created
- Concern: Motion sensor payload format unclear
- Proposal: Use sensorType=motion with value 0/1 and unit="boolean"
- Resolution: Accepted
- Rationale: Keeps single schema for numeric values
- Impact: Consumer treats motion as boolean mapping

---

## Issue #4

- Raised by: Provider
- Topic/Event: all
- Concern: Duplicate events on retry
- Proposal: Use eventId as idempotency key
- Resolution: Accepted
- Rationale: Prevent duplicate processing
- Impact: Consumer must deduplicate by eventId

---

## Issue #5

- Raised by: Consumer
- Topic/Event: all
- Concern: Missing correlationId breaks tracing
- Proposal: correlationId required for all events
- Resolution: Accepted
- Rationale: End-to-end tracing across services
- Impact: Provider must generate correlationId

---

## Issue #6

- Raised by: Provider
- Topic/Event: all
- Concern: Retry and DLQ behavior not defined
- Proposal: Broker retries 3 times then DLQ; details in Lab 03
- Resolution: Accepted
- Rationale: Avoid infinite retries
- Impact: Core Business monitors DLQ metrics

---

# Sign-off v1.0

Provider sign-off: Nguyen Viet Quang  
Consumer sign-off:  
Witness (GV/TA):    
Date: 2026-05-19
