# Design & Architecture

---

## Phase 1 – Design Foundations (Essential for All Roles)

### 1. Core Principles
- **SOLID principles** + when NOT to apply them
- **High Cohesion, Low Coupling**
- **Separation of Concerns**

**Senior insight:** Design for change, avoid speculative design. Over-engineering kills velocity.

### 2. Essential Design Patterns
**Creational:** Factory, Builder, Singleton (know why it's often anti-pattern)
**Structural:** Adapter, Decorator, Facade, Proxy
**Behavioral:** Strategy, Observer, Command, State
**Enterprise:** MVC, Repository, CQRS (basic concept)

**Senior insight:** Patterns are tools, not checklist. Know when patterns become liability in large systems.

---

## Phase 2 – Architecture Fundamentals (System Design Foundation)

### 3. Architecture Styles & Patterns
**Styles:** Monolith → Modular Monolith → Microservices → Serverless
**Patterns:** Layered, Clean/Hexagonal, Event-Driven Architecture
**DDD basics:** Bounded Context, Strategic vs Tactical DDD

### 4. Core Trade-offs
- **CAP Theorem:** Choose 2 — Consistency, Availability, Partition tolerance
- **Scalability patterns:** Horizontal vs Vertical, Stateless design
- **Architecture as constraints:** What you can't do matters more than what you can do

**Senior insight:** Technical debt is a design decision. Understand intentional vs accidental debt.

---

## Phase 3 – Microservices & Distributed Systems (Senior+ Level)

### 5. Microservices Essentials
**Core patterns:** Bounded Context, API Gateway, Service Discovery
**Communication:** REST vs gRPC, Sync vs Async messaging
**Resilience:** Circuit Breaker, Retry, Rate Limiting, Timeout
**Data consistency:** Saga pattern (Choreography vs Orchestration), Idempotency

### 6. Distributed Systems Reality
**Key insight:** Everything fails. Design for failure, not success.
- **Latency is first-class concern** — not just an afterthought
- **Observability-first design** — Logs, metrics, tracing from day 1; see [observability.md](observability.md)
- **Failure modes:** Network partitions, cascading failures, split-brain

**Senior patterns:** Event sourcing, CQRS, Saga, Outbox pattern

---

## Phase 4 – System Design Interview Mastery

### 7. System Design Process (Memorize This!)
1. **Clarify requirements** (5 min) — Functional + Non-functional
2. **Estimate scale** (5 min) — Users, requests, data size
3. **High-level design** (15–20 min) — Major components, data flow
4. **Deep dive** (15–20 min) — Bottlenecks, detailed design
5. **Scale & optimize** (5–10 min) — Handle growth, discuss trade-offs

### 8. Core Scaling Patterns (Essential!)
- **Load Balancing:** High traffic → L7 (HTTP), L4 (TCP), health checks
- **Caching:** Read-heavy workloads → CDN, Redis, write-through/back
- **Database Scaling:** Data growth → Read replicas, sharding, CQRS
- **Async Processing:** Decouple services → Message queues, pub/sub
- **Content Delivery:** Global users → CDN, edge computing
- **API Design:** Rate limiting, reliability → Throttling, pagination, idempotency

### 9. Data Architecture Quick Facts
- **OLTP vs OLAP:** Transactional vs Analytical workloads
- **Consistency:** Strong (ACID) vs Eventual (NoSQL) vs Causal
- **Senior insight:** "Exactly-once delivery is impossible" → Design for at-least-once + idempotency

---

## Phase 5 – Production Engineering (Essential for Senior Roles)

### 10. Testing Strategy
**Test Pyramid:** Unit → Integration → E2E (cost & speed trade-offs)
**Microservices testing:** Contract testing, service virtualization
**Senior insight:** Balance testing vs observability — can't test everything, observe in production

### 11. Deployment & Operations
**Deployment patterns:** Blue-Green, Canary, Rolling deployments
**Infrastructure:** IaC, GitOps, container orchestration basics
**Reliability:** SLA/SLO/SLI, error budgets, chaos engineering concepts
**Disaster Recovery & Backup:**
- **Backup strategy:** full snapshot + incremental; PITR (point-in-time recovery) cho DB quan trọng
- **DR tiers:** Backup/restore → Pilot light → Warm standby → Multi-site / Active-active
- **RTO/RPO:** DR plan phải define rõ RTO/RPO; multi-region thường active-passive (backup/restore, pilot light) cho senior-level systems
- **Chaos engineering:** game days, failover drills để verify DR readiness

---

## Phase 6 – Leadership & Organization (Staff+ Engineer Level)

### 12. Engineering Leadership
**Conway's Law:** Organizations design systems that mirror their communication structures
**Team Topologies:** Stream-aligned teams, enabling teams, platform teams
**Architecture governance:** ADRs, design reviews, technical vision

**Senior insight:** Design systems for team autonomy. Technical decisions are organization decisions.

---

## Phase 7 – Production Mastery (Senior Staff Level)

### 13. Cloud & Performance
**Cloud patterns:** Multi-cloud, serverless vs containers, IaC basics
**Performance:** Caching strategies (LRU, LFU, TTL), auto-scaling, bulkhead patterns
**Resilience:** Circuit breakers, retries, timeouts, chaos engineering principles

### 14. Security Essentials
**Authentication/Authorization:** OAuth2, JWT, Zero Trust principles
**Security fundamentals:** OWASP Top 10, encryption, secure communication
**Compliance awareness:** GDPR, HIPAA implications for system design

### 15. Common System Design Components
- **Message Queues:** Kafka, RabbitMQ, SQS (reliability, ordering, partitioning)
- **Databases:** SQL vs NoSQL choice, sharding strategies, consistency patterns
- **Caching layers:** Redis, Memcached, application cache, cache eviction
- **Search systems:** Elasticsearch, Lucene, indexing strategies
- **File storage:** Object storage (S3), CDN, media processing
- **Monitoring:** Logs, metrics, tracing, alerting systems

### 16. Modernization Patterns
**Legacy evolution:** Strangler Fig, Anti-corruption Layer, Feature Toggles
**Advanced patterns:** Event Sourcing, Advanced CQRS, Edge computing

---

## 📚 Essential References

### Must-Read Books
- [Clean Architecture](pdf/Clean-Architecture.pdf) — Architecture principles
- [System Design Interview](pdf/System-Design-Interview.pdf) — **THE system design book**
- [Building Microservices](pdf/Building-Microservices.pdf) — Microservices patterns
- [Fundamentals of Software Architecture](pdf/Fundamentals-of-software-architecture-an-engineering-approach.pdf) — Architecture engineering approach

### Key Resources
- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [ByteByteGo](https://blog.bytebytego.com/)
- [Architecture Notes](https://architecturenotes.co/)
- [Microservices Patterns](https://microservices.io/patterns/)

---

## 🎯 Common System Design Examples

**URL Shortener (like bit.ly):** Hashing, caching, database sharding, analytics. Focus: encoding algorithm, cache strategy, custom domains.

**Social Media Feed (like Twitter/Instagram):** Fan-out strategies, timeline generation, content ranking. Focus: push vs pull models, celebrity user problem, media storage.

**Chat System (like WhatsApp/Slack):** WebSocket connections, message queues, presence systems. Focus: real-time communication, delivery guarantees, group chat.

**Video Streaming (like YouTube):** CDN, video encoding, metadata storage, recommendation. Focus: video processing pipeline, global distribution, search.

**Ride Sharing (like Uber):** Location services, matching algorithms, real-time tracking. Focus: geospatial indexing, ETAs, surge pricing, driver dispatch.

---

## 🎯 Quick Review Checklist

**System Design Interview (45–60 min):**
- [ ] Clarify requirements (5 min)
- [ ] Estimate scale (5 min)
- [ ] High-level design (15–20 min)
- [ ] Deep dive bottlenecks (15–20 min)
- [ ] Scale discussion (5–10 min)

**Architecture Principles:**
- [ ] SOLID + when not to apply
- [ ] Design patterns as tools, not goals
- [ ] CAP theorem trade-offs
- [ ] Microservices patterns & pitfalls
- [ ] Scalability patterns (caching, load balancing, sharding)

---

## Saga Pattern & Outbox Pattern — Distributed Transactions

### Vấn đề
- Microservices: mỗi service có DB riêng → không thể dùng ACID transactions across services
- **Ví dụ:** Order Service tạo order → Payment Service charge → Inventory Service reserve stock
- Nếu Inventory fail sau khi Payment succeed → inconsistent state!

### Saga Pattern

#### Choreography Saga (Event-driven)

Order Service emits OrderCreated event
→ Payment Service listens, processes payment, emits PaymentProcessed
→ Inventory Service listens, reserves stock, emits StockReserved
→ Order Service listens, confirms order

**Failure scenario:** Payment fails → emits PaymentFailed → Order Service cancels order

**Pros:** Loose coupling, no central coordinator, services independent
**Cons:** Hard to debug (logic distributed across services), risk of cyclic event dependencies

#### Orchestration Saga (Command-driven)

Central Saga Orchestrator sends commands:
1. CreatePayment → Payment Service → success/fail
2. ReserveStock → Inventory Service → success/fail
3. ConfirmOrder → Order Service

**Failure at step 2:**
→ Orchestrator sends RefundPayment (compensating) → Payment Service
→ Orchestrator sends CancelOrder → Order Service

**Pros:** Centralized logic, easy to trace, explicit failure handling
**Cons:** Orchestrator is potential bottleneck, services coupled to orchestrator

#### Compensating Transactions
Every saga step must have a compensating action:
- CreateOrder ↔ CancelOrder
- ChargePayment ↔ RefundPayment
- ReserveInventory ↔ ReleaseInventory

Compensating transactions must be **idempotent** (safe to retry multiple times)

### Outbox Pattern — Reliable Event Publishing

#### Vấn đề
Two operations in sequence (DB save + Kafka publish) are NOT atomic:
- If Kafka fails after DB commit → event lost!
- If DB fails after Kafka publish → duplicate event!

#### Outbox Solution
Atomic approach using a single DB transaction:
1. Save business entity (order) to DB
2. Save outbox event record to `outbox_events` table **in the same transaction**
3. Separate **Outbox Relay** process (or Debezium CDC) reads unpublished events and publishes to Kafka
4. Mark event as published only after Kafka ACK

**Outbox table fields:** id, event_type, topic, payload, created_at, published_at (NULL = pending), retry_count

**Relay options:**
- **Polling Publisher:** scheduled job reads `WHERE published_at IS NULL` → publish → update
- **Debezium CDC:** reads PostgreSQL WAL → captures INSERT on outbox table → publishes to Kafka (real-time, no polling)

**Guarantees:**
- **At-least-once delivery:** event will be published (may duplicate → consumers must be idempotent)
- **Transactional consistency:** business data and event always in sync

### Saga vs 2PC (Two-Phase Commit)
| | 2PC | Saga |
|---|---|---|
| Consistency | Strong (ACID across services) | Eventual |
| Availability | Low (blocking lock) | High |
| Complexity | Simple to understand | Complex (compensations) |
| Performance | Slow | Fast |
| Use case | Small transactions, same cluster | Microservices, distributed |

### CQRS (Command Query Responsibility Segregation)

**Pattern:** Separate the write model (commands) from the read model (queries)
- **Command side:** accepts writes, enforces business rules, emits events
- **Query side:** optimized read models (denormalized, indexed for query patterns)

**Benefits:** Optimize reads/writes independently, different scaling, different storage
**Cost:** Eventual consistency between write and read models, more complexity

**Use when:** Read patterns differ greatly from write structure; need to scale reads independently; building event sourcing

### Event Sourcing
- Store all changes as immutable events, not current state
- Reconstruct current state by replaying events
- **Benefits:** Full audit trail, time-travel (reconstruct at any point), natural fit with CQRS
- **Cost:** Complexity, event schema evolution is hard, eventual consistency

### Interview Questions
- "Làm sao handle distributed transactions trong microservices?" → Saga pattern + Outbox
- "Choreography vs Orchestration Saga?" → Choreography: decoupled, hard to debug; Orchestration: centralized, easier
- "Tại sao cần Outbox Pattern?" → Atomic business data + event publish; prevent lost events
- "Compensating transaction là gì?" → Undo action for each step when saga fails; must be idempotent
- "CQRS khi nào dùng?" → When read/write patterns differ significantly; heavy read scaling needs
