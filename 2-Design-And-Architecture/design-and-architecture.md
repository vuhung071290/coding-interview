# Design & Architecture

---

## Phase 1 – Design Foundations (Essential for All Roles)

### 1. Core Principles
**Must know:**
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
- **CAP Theorem** - Choose 2: Consistency, Availability, Partition tolerance
- **Scalability patterns** - Horizontal vs Vertical, Stateless design
- **Architecture as constraints** - What you can't do matters more than what you can do

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
- **Latency is first-class concern** - Not just an afterthought
- **Observability-first design** - Logs, metrics, tracing from day 1
- **Failure modes:** Network partitions, cascading failures, split-brain

**Senior patterns:** Event sourcing, CQRS, Saga, Outbox pattern

---

## Phase 4 – System Design Interview Mastery

### 7. System Design Process (Memorize This!)
1. **Clarify requirements** (5 min) - Functional + Non-functional
2. **Estimate scale** (5 min) - Users, requests, data size
3. **High-level design** (15-20 min) - Major components, data flow
4. **Deep dive** (15-20 min) - Bottlenecks, detailed design
5. **Scale & optimize** (5-10 min) - Handle growth, discuss trade-offs

### 8. Core Scaling Patterns (Essential!)
• **Load Balancing** - High traffic → L7 (HTTP), L4 (TCP), health checks
• **Caching** - Read-heavy workloads → CDN, Redis, write-through/back
• **Database Scaling** - Data growth → Read replicas, sharding, CQRS
• **Async Processing** - Decouple services → Message queues, pub/sub
• **Content Delivery** - Global users → CDN, edge computing
• **API Design** - Rate limiting, reliability → Throttling, pagination, idempotency

### 9. Data Architecture Quick Facts
- **OLTP vs OLAP:** Transactional vs Analytical workloads
- **Consistency:** Strong (ACID) vs Eventual (NoSQL) vs Causal
- **Senior insight:** "Exactly-once delivery is impossible" → Design for at-least-once + idempotency

---

## Phase 5 – Production Engineering (Essential for Senior Roles)

### 10. Testing Strategy
**Test Pyramid:** Unit → Integration → E2E (cost & speed trade-offs)
**Microservices testing:** Contract testing, service virtualization
**Senior insight:** Balance testing vs observability - can't test everything, observe in production

### 11. Deployment & Operations
**Deployment patterns:** Blue-Green, Canary, Rolling deployments
**Infrastructure:** IaC, GitOps, container orchestration basics
**Reliability:** SLA/SLO/SLI, error budgets, chaos engineering concepts

---

## Phase 6 – Leadership & Organization (Staff+ Engineer Level)

### 12. Engineering Leadership
**Conway’s Law:** Organizations design systems that mirror their communication structures
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
**Essential building blocks for interviews:**
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
- [Clean Architecture](pdf/Clean-Architecture.pdf) - Architecture principles
- [System Design Interview](pdf/System-Design-Interview.pdf) - **THE system design book**
- [Building Microservices](pdf/Building-Microservices.pdf) - Microservices patterns
- [Fundamentals of Software Architecture](pdf/Fundamentals-of-software-architecture-an-engineering-approach.pdf) - Architecture engineering approach

### Key Resources
**System Design Practice:**
- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [ByteByteGo](https://blog.bytebytego.com/)
- [High Scalability](http://highscalability.com/)

**Architecture Knowledge:**
- [Architecture Notes](https://architecturenotes.co/)
- [Microservices Patterns](https://microservices.io/patterns/)

---

---

## 🎯 Common System Design Examples

**URL Shortener (like bit.ly):**
- Key patterns: Hashing, caching, database sharding, analytics
- Focus: Encoding algorithm, cache strategy, custom domains

**Social Media Feed (like Twitter/Instagram):**
- Key patterns: Fan-out strategies, timeline generation, content ranking
- Focus: Push vs pull models, celebrity user problem, media storage

**Chat System (like WhatsApp/Slack):**
- Key patterns: WebSocket connections, message queues, presence systems
- Focus: Real-time communication, message delivery guarantees, group chat

**Video Streaming (like YouTube):**
- Key patterns: CDN, video encoding, metadata storage, recommendation
- Focus: Video processing pipeline, global distribution, search

**Ride Sharing (like Uber):**
- Key patterns: Location services, matching algorithms, real-time tracking
- Focus: Geospatial indexing, ETAs, surge pricing, driver dispatch

---

## 🎯 Quick Review Checklist

**System Design Interview (45-60 min):**
- [ ] Clarify requirements (5 min)
- [ ] Estimate scale (5 min)
- [ ] High-level design (15-20 min)
- [ ] Deep dive bottlenecks (15-20 min)
- [ ] Scale discussion (5-10 min)

**Architecture Principles:**
- [ ] SOLID + when not to apply
- [ ] Design patterns as tools, not goals
- [ ] CAP theorem trade-offs
- [ ] Microservices patterns & pitfalls
- [ ] Scalability patterns (caching, load balancing, sharding)

**Senior-Level Insights:**
- [ ] Architecture is constraints
- [ ] Conway's Law implications
- [ ] Observability-first design
- [ ] Failure-aware system design
