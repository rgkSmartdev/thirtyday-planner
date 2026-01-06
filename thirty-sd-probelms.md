# 30 System Design Problems for Java Tech Lead Role

Here's a curated list of 30 system design problems, organized by difficulty and category.  Work on one per day to build comprehensive system design skills. 

---

## **Week 1: Fundamentals & Common Applications (Day 1-7)**

### Day 1: **URL Shortener (e.g., bit.ly)**
- **Focus**: Hashing, database design, scalability basics
- **Key Concepts**:  Base62 encoding, collision handling, analytics
- **Java Skills**: Spring Boot REST API, Redis caching, PostgreSQL

### Day 2: **Pastebin / Code Sharing Service**
- **Focus**: Object storage, expiration policies, access control
- **Key Concepts**:  TTL, S3/blob storage, syntax highlighting
- **Java Skills**:  Scheduled tasks, AWS SDK, MongoDB

### Day 3: **Rate Limiter**
- **Focus**: Algorithm design, distributed systems
- **Key Concepts**: Token bucket, sliding window, Redis
- **Java Skills**: Interceptors, AOP, Redis operations

### Day 4: **Parking Lot System**
- **Focus**: OOP design, state management
- **Key Concepts**: Design patterns (Strategy, Factory), concurrency
- **Java Skills**:  Multithreading, synchronization, enum design

### Day 5: **Book My Show / Movie Ticket Booking**
- **Focus**: Concurrency, booking conflicts, payments
- **Key Concepts**:  Pessimistic/optimistic locking, seat reservation
- **Java Skills**: JPA transactions, distributed locks, idempotency

### Day 6: **Task Scheduler / Cron Job System**
- **Focus**: Job queues, scheduling algorithms
- **Key Concepts**: Priority queues, delayed execution, Quartz
- **Java Skills**: Spring @Scheduled, RabbitMQ, delay queues

### Day 7: **Notification System**
- **Focus**: Multi-channel delivery, prioritization
- **Key Concepts**: Push notifications, SMS, email, fan-out
- **Java Skills**:  Async processing, Kafka, FCM/SNS integration

---

## **Week 2: Social & Content Platforms (Day 8-14)**

### Day 8: **Instagram / Image Sharing Platform**
- **Focus**: Media storage, CDN, feed generation
- **Key Concepts**: Image processing, S3, CloudFront, fan-out on write
- **Java Skills**: ImageIO, async uploads, Redis sorted sets

### Day 9: **Twitter / Microblogging Platform**
- **Focus**: Timeline generation, follower relationships
- **Key Concepts**: Fan-out on write vs read, graph databases
- **Java Skills**: Neo4j, Cassandra, cache warming

### Day 10: **Facebook News Feed**
- **Focus**:  Personalized ranking, edge rank algorithm
- **Key Concepts**: ML ranking, real-time updates, graph traversal
- **Java Skills**: Redis pub/sub, GraphQL, pagination

### Day 11: **YouTube / Video Streaming Platform**
- **Focus**: Video transcoding, adaptive streaming
- **Key Concepts**: HLS/DASH, CDN, video processing pipeline
- **Java Skills**: FFmpeg integration, chunked uploads, S3 multipart

### Day 12: **LinkedIn / Professional Network**
- **Focus**: Connection degrees, job recommendations
- **Key Concepts**: Graph algorithms (BFS), search indexing
- **Java Skills**:  Elasticsearch, graph traversal, recommendation engines

### Day 13: **Reddit / Discussion Forum**
- **Focus**:  Voting system, hot/trending algorithms
- **Key Concepts**: Wilson score, comment trees, sharding
- **Java Skills**:  Recursive queries, Postgres JSONB, caching strategies

### Day 14: **WhatsApp / Chat Messaging System**
- **Focus**: Real-time messaging, message delivery
- **Key Concepts**: WebSockets, message queues, read receipts
- **Java Skills**:  Spring WebSocket, STOMP, MongoDB for history

---

## **Week 3: E-commerce & Marketplace (Day 15-21)**

### Day 15: **Amazon / E-commerce Platform**
- **Focus**: Product catalog, inventory, order processing
- **Key Concepts**:  Microservices, SAGA pattern, eventual consistency
- **Java Skills**: Spring Cloud, distributed transactions, Kafka

### Day 16: **Payment Gateway**
- **Focus**: Transaction processing, idempotency, reconciliation
- **Key Concepts**:  Two-phase commit, PCI compliance, retry logic
- **Java Skills**:  Stripe/PayPal SDK, transaction management, encryption

### Day 17: **Shopping Cart System**
- **Focus**: Session management, abandoned cart recovery
- **Key Concepts**: Redis sessions, cookie management, persistence
- **Java Skills**: Spring Session, scheduled cleanup, state machine

### Day 18: **Inventory Management System**
- **Focus**: Stock tracking, warehouse optimization
- **Key Concepts**: FIFO/LIFO, reorder points, distributed inventory
- **Java Skills**: JPA auditing, batch processing, event sourcing

### Day 19: **Food Delivery App (Uber Eats / DoorDash)**
- **Focus**: Location tracking, matching algorithm, ETA
- **Key Concepts**: Geospatial indexing, routing, real-time updates
- **Java Skills**: PostGIS, Redis geospatial, WebSockets

### Day 20: **Auction System (eBay)**
- **Focus**: Bidding logic, real-time updates, fairness
- **Key Concepts**:  Optimistic locking, WebSockets, time sync
- **Java Skills**:  Scheduled bid closing, concurrent updates, Redis sorted sets

### Day 21: **Coupon/Promotion System**
- **Focus**:  Validation rules, usage limits, fraud prevention
- **Key Concepts**:  Rule engine, distributed counters, idempotency
- **Java Skills**:  Drools, Redis atomic operations, audit logging

---

## **Week 4: Infrastructure & Enterprise (Day 22-28)**

### Day 22: **Distributed Cache (Redis/Memcached)**
- **Focus**: Cache eviction, consistency, partitioning
- **Key Concepts**: LRU/LFU, consistent hashing, cache-aside
- **Java Skills**: Jedis/Lettuce, cache annotations, serialization

### Day 23: **API Gateway**
- **Focus**:  Routing, authentication, rate limiting
- **Key Concepts**: Load balancing, circuit breaker, API versioning
- **Java Skills**: Spring Cloud Gateway, Zuul, JWT validation

### Day 24: **Load Balancer**
- **Focus**: Distribution algorithms, health checks
- **Key Concepts**: Round-robin, least connections, sticky sessions
- **Java Skills**:  Nginx config, HAProxy, service discovery

### Day 25: **Distributed File Storage (Google Drive / Dropbox)**
- **Focus**: File chunking, sync, version control
- **Key Concepts**:  Block storage, deduplication, conflict resolution
- **Java Skills**:  Chunked upload/download, checksums, event-driven sync

### Day 26: **Search Engine (Elasticsearch-like)**
- **Focus**: Indexing, ranking, query processing
- **Key Concepts**:  Inverted index, TF-IDF, sharding
- **Java Skills**: Lucene, tokenization, custom analyzers

### Day 27: **Metrics & Monitoring System (Prometheus-like)**
- **Focus**: Time-series data, aggregation, alerting
- **Key Concepts**: Push vs pull, retention policies, downsampling
- **Java Skills**:  Micrometer, InfluxDB, scheduled aggregations

### Day 28: **Log Aggregation System (ELK Stack)**
- **Focus**: Log collection, parsing, visualization
- **Key Concepts**: Log shipping, structured logging, retention
- **Java Skills**: Logback, Filebeat integration, Elasticsearch queries

---

## **Week 5: Advanced & Specialized (Day 29-30 + Bonus)**

### Day 29: **Distributed Message Queue (Kafka/RabbitMQ)**
- **Focus**: Pub/sub, message ordering, durability
- **Key Concepts**: Partitioning, consumer groups, exactly-once semantics
- **Java Skills**:  Kafka Streams, Spring AMQP, dead letter queues

### Day 30: **Ride-Sharing Platform (Uber / Lyft)**
- **Focus**: Real-time matching, surge pricing, routing
- **Key Concepts**:  Geohashing, quadtree, dynamic pricing
- **Java Skills**: Geospatial algorithms, Redis geospatial, routing APIs

---

## **Bonus Problems (Continue Practice)**

### Bonus 1: **Stock Trading Platform**
- **Focus**: Order matching, real-time quotes, compliance
- **Key Concepts**: Order book, FIFO matching, latency optimization

### Bonus 2: **Content Delivery Network (CDN)**
- **Focus**: Edge caching, geo-distribution, cache invalidation
- **Key Concepts**:  DNS routing, anycast, cache hierarchy

### Bonus 3: **Recommendation Engine**
- **Focus**: Collaborative filtering, personalization
- **Key Concepts**: Matrix factorization, cold start, A/B testing

### Bonus 4: **Hotel Booking System**
- **Focus**: Availability calendar, pricing, overbooking
- **Key Concepts**: Time-based locking, dynamic pricing, cancellations

### Bonus 5: **Online Code Editor (LeetCode/HackerRank)**
- **Focus**: Code execution, sandboxing, resource limits
- **Key Concepts**:  Containerization, timeout handling, output streaming

---

## **How to Approach Each Problem**

### **1. Requirements Gathering (10 min)**
- Functional requirements (features)
- Non-functional requirements (scale, latency, availability)
- Constraints and assumptions

### **2. High-Level Design (15 min)**
- Draw component diagram
- Identify major services/modules
- Define APIs and data flow

### **3. Deep Dive (20 min)**
- Database schema design
- Choose tech stack (Java frameworks, databases, caches)
- Address bottlenecks and scaling strategies
- Discuss trade-offs

### **4. Advanced Topics (10 min)**
- Monitoring and logging
- Security considerations
- Disaster recovery
- CI/CD pipeline

### **5. Java-Specific Implementation (5 min)**
- Discuss Spring Boot microservices architecture
- Mention relevant Java libraries and frameworks
- Code structure and design patterns

---

## **Key Java Tech Lead Focus Areas**

For each design, emphasize: 

✅ **Microservices Architecture**: Spring Boot, Spring Cloud
✅ **Messaging**:  Kafka, RabbitMQ, Spring Integration
✅ **Caching**: Redis, Hazelcast, Caffeine
✅ **Databases**: PostgreSQL, MongoDB, Cassandra
✅ **API Design**: REST, GraphQL, gRPC
✅ **Observability**: Prometheus, Grafana, ELK
✅ **Security**: OAuth2, JWT, Spring Security
✅ **Testing**:  JUnit, Mockito, TestContainers
✅ **DevOps**: Docker, Kubernetes, Jenkins

---

## **Recommended Resources**

- **Book**: "Designing Data-Intensive Applications" by Martin Kleppmann
- **Website**: https://github.com/donnemartin/system-design-primer
- **YouTube**:  Gaurav Sen, Tech Dummies Narendra L
- **Practice**: Draw. io for diagrams, LucidChart

---

Would you like me to deep-dive into any specific problem or provide a detailed solution template for one of these?  Good luck with your preparation!  🚀
