# Java Tech Lead Interview Planner (30–40 Days) — E‑commerce Focus (Java 11+)

**Daily time (6–8 hrs)**
- **DSA (2.5–3h):** TUF pattern → LeetCode timed practice → mistake log
- **System Design (2.5–3h):** 1 design/day + 15–20 min verbal presentation (record)
- **Java/Spring/Kafka/AWS (1–1.5h):** TL-level depth (transactions, concurrency, reliability)
- **Review (0.5h):** notes + flashcards + “what I’d do differently”

**Core deliverables by end**
- 12–15 **System design 1-pagers** (e‑commerce heavy)
- 1 “**Architecture & trade-offs**” cheat sheet
- 1 “**Kafka + Idempotency + Outbox**” cheat sheet
- 1 “**DB/Indexing/Transactions**” cheat sheet
- 1 “**DSA patterns**” cheat sheet
- 6–10 **mock interviews** (recorded)

---

## Week 1 — Foundations: DSA basics + system design template + Java credibility

### Day 1 — Setup + Arrays/Hashing + Design Template
**DSA (LeetCode)**
- Two Sum
- Contains Duplicate
- Valid Anagram  
**System Design**
- Build your answer template; Design: URL Shortener (v1)  
**Java/Spring**
- Java 11 recap (streams/Optional best practices)  
**Deliverable:** System design template (1 page) + DSA mistake log

### Day 2 — Two Pointers + API Design
**DSA**
- Valid Palindrome
- Two Sum II
- Container With Most Water (Medium)  
**System Design**
- E‑commerce API guidelines (pagination, filtering, versioning, idempotency keys)  
**Java/Spring**
- Spring Boot layering + validation + global exception handling

### Day 3 — Sliding Window
**DSA**
- Best Time to Buy and Sell Stock
- Longest Substring Without Repeating Characters (Medium)
- Longest Repeating Character Replacement (Medium)  
**System Design**
- Catalog Read Service + Redis cache (cache-aside, TTL, invalidation, stampede)  
**Java**
- HashMap internals + equals/hashCode contract

### Day 4 — Stack + Monotonic Stack
**DSA**
- Valid Parentheses
- Min Stack
- Daily Temperatures (Medium)  
**System Design**
- Rate Limiter (token bucket vs sliding window; Redis approach)  
**Java**
- synchronized vs volatile vs Atomic* (when/why)

### Day 5 — Linked List Basics
**DSA**
- Reverse Linked List
- Linked List Cycle
- Merge Two Sorted Lists  
**System Design**
- Cart service (guest cart, merge on login, TTL, pricing snapshot vs live)  
**Hibernate**
- N+1, fetch joins, pagination pitfalls

### Day 6 — Trees Traversal (BFS/DFS)
**DSA**
- Maximum Depth of Binary Tree
- Binary Tree Level Order Traversal (Medium)
- Invert Binary Tree  
**System Design**
- Observability baseline (logs/metrics/traces; correlation IDs across checkout)  
**Java**
- Thread pools (types, sizing, rejection policies)

### Day 7 — Mock #1 + Consolidation
**Mock**
- DSA: 2 mediums in 60 minutes
- System design: Re-present Cart or URL shortener (30–40 min)  
**Deliverable:** Week 1 cheat sheet (DSA patterns + design checklist)

---

## Week 2 — Ramp-up: Binary search, graphs, intervals + core e‑commerce services

### Day 8 — Heaps / Top-K
**DSA**
- Kth Largest Element in an Array (Medium)
- Top K Frequent Elements (Medium)  
**System Design**
- Search + filtering (indexing strategy, facets, sorting, caching)  
**Java**
- PriorityQueue usage + comparator pitfalls

### Day 9 — Graph BFS/DFS
**DSA**
- Number of Islands (Medium)
- Rotting Oranges (Medium)  
**System Design**
- Order placement overview (services + events; boundaries)  
**Kafka**
- partitions, ordering, consumer groups

### Day 10 — Binary Search (rotated + boundaries)
**DSA**
- Find Minimum in Rotated Sorted Array (Medium)
- Search in Rotated Sorted Array (Medium)  
**System Design**
- Inventory reservation (avoid oversell: optimistic lock vs reservation tokens)  
**DB/Hibernate**
- isolation basics + deadlocks awareness

### Day 11 — Binary Search on Answer Space
**DSA**
- Koko Eating Bananas (Medium)
- Capacity To Ship Packages Within D Days (Medium)  
**System Design**
- Pricing service (base price + promos + dynamic pricing; cache invalidation)  
**AWS**
- RDS vs Dynamo-style thinking; ElastiCache patterns

### Day 12 — Intervals
**DSA**
- Merge Intervals (Medium)
- Non-overlapping Intervals (Medium)
- Meeting Rooms II (Medium, if time)  
**System Design**
- Promotions/coupons engine (eligibility rules, caching, abuse prevention)  
**Java/Spring**
- Transaction boundaries + propagation (conceptual)

### Day 13 — Backtracking Intro
**DSA**
- Subsets (Medium)
- Permutations (Medium)
- Combination Sum (Medium, optional if time)  
**System Design**
- Notification system (email/SMS/push; retries, DLQ, templates, preferences)  
**Kafka**
- retries, DLQ, poison messages, idempotency basics

### Day 14 — Mock #2
**Mock**
- DSA: 3 mediums in 75 minutes
- System design: Pricing or Promotions (60 minutes)  
**Deliverable:** 4 polished system design 1‑pagers

---

## Week 3 — Mastery: Checkout, payments, OMS, returns + deep dives

### Day 15 — Checkout (Full)
**DSA**
- Subarray Sum Equals K (Medium)
- Longest Substring Without Repeating Characters (Medium)  
**System Design**
- Checkout end-to-end (cart→pricing→inventory→payment→order)  
Cover: sagas, outbox, idempotency, partial failures  
**Java**
- CompletableFuture patterns + timeouts (awareness)

### Day 16 — Payment System (Deep)
**DSA**
- House Robber (Medium)
- Coin Change (Medium)  
**System Design**
- Payments: idempotency keys, ledger, reconciliation, refund flows  
**DB**
- unique constraints for idempotency; exactly-once myth

### Day 17 — Order Management System (OMS)
**DSA**
- Course Schedule (Medium)
- Course Schedule II (Medium)  
**System Design**
- OMS: order states, events, audit trail, reprocessing, admin queries  
**Kafka**
- schema evolution/versioning; consumer compatibility

### Day 18 — Inventory (Deep)
**DSA**
- 01 Matrix (Medium)
- Max Area of Island (Medium)  
**System Design**
- Inventory correctness: reservations, release, reaper jobs, eventual reconciliation  
**Hibernate**
- optimistic locking with @Version; retry strategy

### Day 19 — Returns & Refunds
**DSA**
- Jump Game (Medium)
- Gas Station (Medium)  
**System Design**
- Returns/refunds pipeline: approvals, reverse pickup, refund idempotency  
**Resilience**
- timeouts, retries with jitter, circuit breakers

### Day 20 — Shipping Integration
**DSA**
- Longest Common Subsequence (Medium)
- Unique Paths (Medium)  
**System Design**
- Shipping aggregator: carrier APIs, rate limits, webhooks, retries, auditing  
**Java**
- HTTP client best practices + connection pooling

### Day 21 — Mock #3 (Fuller loop)
**Mock**
- DSA: 2 mediums in 60 minutes
- System design: Checkout or Payments (75 minutes)
- Java/Kafka deep dive: 45 minutes  
**Deliverable:** “Top 25 trade-offs” one-pager

---

## Week 4 — Performance: machine coding + rapid design + behavioral TL

### Day 22 — Machine Coding #1: Rate Limiter Library (Java)
**Build**
- token bucket + tests + thread safety + config  
**System Design**
- distributed rate limiting with Redis + gateway  
**Behavioral**
- write 2 STAR stories (incident + conflict)

### Day 23 — Machine Coding #2: In-memory Cache (LRU + TTL)
**Build**
- LRU+TTL + cleanup strategy + tests  
**System Design**
- cache stampede/hot keys mitigation  
**Behavioral**
- write 2 STAR stories (mentoring + influence)

### Day 24 — Machine Coding #3: Kafka Consumer Template
**Build**
- retries, DLQ, idempotency store, metrics hooks  
**System Design**
- consumer lag/backpressure/rebalancing strategies  
**Behavioral**
- write 2 STAR stories (migration + delivery trade-off)

### Day 25 — Behavioral Day (Tech Lead)
**Prep**
- 8 STAR stories total; practice answering aloud  
**System Design**
- “How I make architecture decisions” talk track (10 minutes)  
**Java TL**
- prod debugging checklist (latency, CPU, memory, Kafka lag)

### Day 26 — System Design Speed Drills (4 × 30 min)
- Cart
- Checkout
- Payments
- Search  
**Deliverable:** 4 short outlines; focus on structure + trade-offs

### Day 27 — DSA Speed Day
**Timed**
- 5 mixed problems (mostly mediums)  
**Deliverable:** “Top 10 bug types I make” checklist

### Day 28 — Mock #4 (Full loop)
- DSA 60
- System design 75
- Java/Spring/Kafka 45
- Behavioral 45

### Day 29 — Weakness Repair
- Re-do 4 previously missed/slow DSA problems (timed)
- Redo 1 system design where you struggled (present again)

### Day 30 — Mock #5 + Final Notes
**Mock**
- 3 mediums in 75 minutes
- 1 full system design (75 minutes)  
**Deliverable:** Final cheat sheets (DSA + design + Kafka/DB)

---

# Optional Extension to 40 Days (Highly recommended)

### Day 31 — Mock #6 (System design only)
- Checkout (60–75 min)
- Search (45–60 min)

### Day 32 — DSA Medium Reinforcement
- 4 mediums focused on your worst 2 patterns

### Day 33 — Production Excellence Day
- Prepare “how to debug”: high latency, high CPU, memory leak, Kafka lag, DB deadlock
- Practice explaining step-by-step with tools and hypotheses

### Day 34 — Mock #7 (Full loop)

### Day 35 — Commerce Deep Dive: Inventory vs Payment Consistency
- Exactly-once processing reality
- Outbox/inbox + dedupe + compensation

### Day 36 — Mock #8 (DSA only)
- 6 problems in 3 hours (timed)

### Day 37 — Mock #9 (Behavioral + Leadership)
- conflict, mentoring, roadmap, technical vision, incident leadership

### Day 38 — Mock #10 (Full loop)

### Day 39 — Patch + Polish
- Redo 2 system designs + 6 DSA re-attempts from mistake log

### Day 40 — Final Simulation + Rest Plan
- One last full loop
- Create day-before checklist: sleep, setup, cheat sheets, story bank

---

## System Design Prompts (E‑commerce) — master these
1. Product catalog + caching
2. Search + filtering + facets
3. Cart service
4. Pricing + promotions engine
5. Checkout orchestration (saga/outbox)
6. Payment system (ledger + idempotency + refunds)
7. Inventory reservation (oversell prevention)
8. Order Management System
9. Shipping integration service
10. Notifications (email/SMS/push)
11. Returns/refunds workflow
12. Observability pipeline (logs/metrics/traces)

## DSA Patterns to master (TUF-aligned)
- Arrays/Hashing + prefix sums
- Two pointers
- Sliding window
- Stack/monotonic stack
- Binary search (incl answer space)
- Linked list patterns
- Trees BFS/DFS
- Graph BFS/DFS + topo
- Heaps / top-K
- Intervals
- Backtracking
- DP (1D + basic 2D)
