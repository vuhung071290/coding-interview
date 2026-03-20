# Design & Architecture

---

## Phase 1 – Software Design Foundations
Xây nền tư duy thiết kế và design taste

### 1. Core Software Principles
Kiến thức nền tảng
- SOLID
- DRY, KISS, YAGNI
- Separation of Concerns
- High Cohesion – Low Coupling

Senior layer
- Khi không nên áp dụng SOLID
- Over-engineering vs Under-engineering
- Design for change vs Speculative design

---

### 2. Design Patterns
Pattern là tool, không phải checklist

Creational
- Factory, Abstract Factory
- Builder
- Singleton (anti-pattern discussion)

Structural
- Adapter
- Decorator
- Facade
- Proxy

Behavioral
- Strategy
- Observer
- Command
- State

Enterprise / Application-level
- MVC / MVP / MVVM
- Repository
- CQRS (basic)

Senior layer
- Pattern vs framework feature
- Pattern decay trong hệ thống lớn
- Khi nào pattern trở thành liability

References  
Books
- [Clean Code](./pdf/Clean-Code.pdf)
- [Head First Design Patterns](./pdf/Head-First-Design-Patterns.pdf)
- [Design Patterns (GoF)](./pdf/Design-Patterns-by-Gang-of-Four.pdf)

Web
- https://www.interviewbit.com/blog/principles-of-software-engineering/
- https://refactoring.guru/design-patterns/java

---

## Phase 2 – Software Architecture Fundamentals
Hiểu cách tổ chức hệ thống và tư duy kiến trúc

### 3. Architecture Styles & Patterns
Architecture styles
- Monolith
- Modular Monolith
- Microservices
- Serverless

Architecture patterns
- Layered Architecture
- Clean / Hexagonal / Onion
- Event-Driven Architecture
- Cell-Based Architecture

DDD overview
- Domain / Application / Infrastructure
- Bounded Context
- Strategic vs Tactical DDD

---

### 4. Architecture Principles & Trade-offs
Core
- CAP Theorem
- Scalability, Availability, Consistency
- Stateless vs Stateful

Senior layer
- Architecture là tập hợp các constraint
- Technical Debt (intentional vs accidental)
- Cost of change (myth vs reality)

References  
Books
- [Fundamentals of Software Architecture](./pdf/Fundamentals-of-software-architecture-an-engineering-approach.pdf)
- [Software Architecture: The Hard Parts](./pdf/Software-Architecture-The-Hard-Parts.pdf)
- [Clean Architecture](./pdf/Clean-Architecture.pdf)
- [Domain-Driven Design](./pdf/Domain-Driven-Design.pdf)

Web
- https://architecturenotes.co/
- https://herbertograca.com/2017/07/03/the-software-architecture-chronicles/
- https://github.com/wso2/reference-architecture/blob/master/reference-architecture-cell-based.md
- https://eda-visuals.boyney.io/

---

## Phase 3 – Microservices & Distributed Systems
Distributed thinking và failure-aware design

### 5. Microservices Design
Core concepts
- Bounded Context
- API Gateway
- Service Discovery

Communication
- REST vs gRPC
- Sync vs Async
- Messaging

Resilience patterns
- Circuit Breaker
- Retry
- Rate Limiting
- Timeout

Data consistency
- Saga (Choreography vs Orchestration)
- Event-driven microservices
- Idempotency

Infrastructure
- Docker
- Kubernetes
- Service Mesh (concept)

Senior layer
- Distributed failure modes
- Latency là first-class concern
- Observability-first design

References  
Books
- [Building Microservices](./pdf/Building-Microservices.pdf)
- [Microservices Patterns](./pdf/Microservices-Patterns-With-Example-In-Java.pdf)
- [Building Event-Driven Microservices](./pdf/Building-Event-Driven-Microservices-2nd-Edition.pdf)

Web
- https://microservices.io/patterns/

---

## Phase 4 – System Design & Data Architecture
System design interview và production scale

### 6. System Design Fundamentals
Design process
1. Clarify requirements
2. Define constraints
3. High-level design
4. Deep dive (hot paths)
5. Bottlenecks và trade-offs

Scalability patterns
- Load Balancer
- Cache
- Sharding
- Replication

---

### 7. Data Architecture
- OLTP vs OLAP
- Data Warehouse vs Data Lake
- Stream Processing
- Event log as source of truth

Senior layer
- Exactly-once is a lie
- Schema evolution
- Backfill và reprocessing

References  
Books
- [Designing Data-Intensive Applications](./pdf/Designing-Data-Intensive-Applications.pdf)
- [System Design Interview Vol 1](./pdf/System-Design-Interview.pdf)
- [System Design Interview Vol 2](./pdf/System-Design-Interview-Volume-2.pdf)

Web (Core)
- https://github.com/donnemartin/system-design-primer
- https://github.com/ByteByteGoHq/system-design-101
- https://www.techinterviewhandbook.org/system-design/

Web (Supplement)
- https://www.educative.io/blog/complete-guide-to-system-design
- https://github.com/madd86/awesome-system-design

Blogs / Video
- https://blog.bytebytego.com/
- https://www.youtube.com/c/ByteByteGo
- https://www.youtube.com/@hnasr

---

## Phase 5 – Quality, Testing & Delivery
Build, deploy, operate

### 8. Testing Architecture
- Unit, Integration, Contract, E2E
- Test Pyramid
- TDD, BDD
- Mutation Testing
- Shift-left testing

Senior layer
- Testing vs Observability
- Contract testing trong microservices

References
- [Test Driven Development by Example](pdf/Test-Driven-Development-By-Example.pdf)
- [BDD in Action](./pdf/BDD-In-Action-Second-Edition.pdf)

---

### 9. DevOps & Continuous Delivery
- CI/CD pipelines
- Deployment strategies: Canary, Blue-Green, Rolling
- Infrastructure as Code
- GitOps
- Continuous Testing

References
- [Site Reliability Engineering](./pdf/Site-Reliability-Engineering.pdf)
- [Software Engineering at Google](./pdf/Software-Engineering-at-Google.pdf)

---

## Phase 6 – Engineering Organization & Leadership
Senior engineer lên Tech Lead / Architect

### 10. Engineering Practices
- Code Review
- Pair Programming
- Trunk-based Development
- Architecture Decision Record

---

### 11. Team & Organization Design
- Conway’s Law
- Team Topologies
- Platform Engineering

Senior layer
- Align architecture với organization
- Design cho autonomy

References
- [Modern Software Engineering](./pdf/Modern-Software-Engineering-Doing-What-Works-to-Build-Better-Software-Faster.pdf)
- [Team Topologies](./pdf/Team-Topologies.pdf)

---

### 12. Architecture Governance
- Architecture Review Board
- Coding Standards
- Trade-off analysis
- Mentorship và technical vision

References
- [The Clean Coder](./pdf/The-Clean-Coder.pdf)
- [Software Engineering at Google](./pdf/Software-Engineering-at-Google.pdf)

---

## Phase 7 – Cloud, Performance & Security
Production readiness

### 13. Cloud & Platform Architecture
- Cloud-native
- Multi-cloud, Hybrid-cloud
- Serverless vs Container vs VM
- IaC: Terraform, Helm, ArgoCD, Pulumi

---

### 14. Performance & Resilience
- Caching strategies: TTL, LRU, LFU
- Auto-scaling
- Bulkhead, Backpressure
- Chaos Engineering
- SLA, SLO, SLI

---

### 15. Security & Compliance
- Authentication, Authorization
- OAuth2, JWT
- Zero Trust
- Encryption, Data Masking
- OWASP Top 10
- GDPR, HIPAA

References
- https://owasp.org/
- https://cloudsecurityalliance.org/

---

## Phase 8 – Modernization & Migration
Legacy system evolution

### 16. Modernization Patterns
- Strangler Fig
- Anti-Corruption Layer
- Feature Toggle
- Canary Release

Advanced concepts
- Event Sourcing
- Advanced CQRS
- Edge, Fog Computing

---

## Phase 9 – Case Studies & Self-Assessment
Củng cố và kiểm tra kiến thức

### 17. Case Studies
- Netflix: Chaos Engineering
- Uber: Cell-based Architecture
- Twitter: Monolith to Microservices
- AWS: S3 Outage Analysis

---

### 18. Review Questions
- Làm sao scale hệ thống 10x
- Trade-off REST vs gRPC
- Rollback database migration thế nào
- Khi nào dùng Terraform thay vì CloudFormation
