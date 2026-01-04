# 1‑Page System Design Interview Template (Copy/Paste)

## A) Clarify Requirements (2–5 min)
**Functional**
- Core user actions (list 3–6)
- Admin/ops needs (if any)
- Data retention / audit needs

**Non‑functional**
- Latency target (p50/p95)
- Availability (e.g., 99.9% / 99.99%)
- Consistency needs (strong vs eventual; where?)
- Security/compliance (PII, PCI, GDPR)
- Cost constraints (if mentioned)

**Out of scope**
- Explicitly state what you won’t design today

---

## B) Back‑of‑the‑Envelope Scale (3–5 min)
- DAU/MAU:
- Peak RPS (reads/writes):
- Data size (per day/month/year):
- Hot keys / hotspots (celebrity products, flash sale):
- Growth assumptions:

---

## C) Define APIs / Contracts (5–8 min)
List 3–6 APIs with:
- Method + path
- Request/response fields (only important ones)
- Idempotency key (where needed)
- Pagination/filtering/sorting
- Error model (standard codes + message + traceId)

---

## D) Data Model (5–8 min)
Entities + keys + critical indexes:
- Entity:
  - Primary key:
  - Important fields:
  - Indexes (composite if needed):
  - Cardinality/relationships:

Notes:
- What is source of truth?
- Any denormalized read models?

---

## E) High‑Level Architecture (10–15 min)
Draw components + data flow:
- Clients → API Gateway/LB → Services
- Datastore(s)
- Cache(s)
- Message broker/event bus
- Background workers

Call out:
- Sync vs async paths
- Read path vs write path

---

## F) Deep Dives (pick 2–3; 15–25 min)
### 1) Storage & Scaling
- DB choice (RDBMS/NoSQL) & why
- Indexing strategy
- Partitioning/sharding key (if needed)
- Read replicas / CQRS (if needed)

### 2) Caching
- What to cache (objects, pages, query results)
- Cache‑aside vs write‑through
- TTL + invalidation approach
- Stampede & hot key mitigation

### 3) Messaging / Kafka
- Topics + keys + partitions
- Consumer groups
- Retry strategy + DLQ
- Idempotency + dedupe
- Outbox pattern (if DB + Kafka)

### 4) Consistency & Transactions
- Transaction boundaries
- Saga vs 2PC (usually saga)
- Compensation actions
- Exactly‑once expectations (practical)

### 5) Security
- AuthN/AuthZ (JWT/OAuth2)
- Secrets management
- PII handling + encryption at rest/in transit

---

## G) Reliability / Failure Modes (5–10 min)
- Timeouts, retries (with jitter), circuit breaker
- Rate limiting
- Backpressure
- Degradation strategy (what still works when dependencies fail?)
- Data recovery (replay events, reprocess DLQ)

---

## H) Observability & Ops (3–8 min)
- Logs (structured) + correlationId/traceId
- Metrics (RED/USE)
- Tracing (OpenTelemetry)
- SLOs + alerts
- Runbooks

---

## I) Trade‑offs & Alternatives (3–8 min)
- Option A vs B and why chosen
- Costs & operational complexity
- Future improvements / phased rollout

---

# 2) DSA Mistake Log Template (Copy/Paste Table)

> Keep this as a living document. Update after every practice session.

| Date | Problem | LC # | Pattern (TUF) | Difficulty | Time Taken | Result (Solved/Partial/Fail) | Main Mistake Type | Root Cause (1 line) | Fix / Rule to Remember | Re‑attempt Date | Re‑attempt Result |
|------|---------|------|---------------|------------|------------|------------------------------|------------------|---------------------|------------------------|-----------------|------------------|
|      |         |      |               |            |            |                              | (edge case / boundary / complexity / wrong DS / logic / syntax) |                     |                        |                 |                  |

## Suggested “Mistake Type” tags (use consistently)
- **Edge case** (empty input, duplicates, overflow,

