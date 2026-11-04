# Backend Engineering Interview Preparation

> Tài liệu ôn tập toàn diện cho Senior Backend Engineer — từ CS fundamentals đến AI/LLM integration

---

## Lộ trình theo thời gian

Chọn lộ trình phù hợp với lịch interview của bạn:

### ⚡ 1 tuần — Chỉ những gì bắt buộc
Focus vào các phần **🔴 Must** dưới đây. Đủ để pass hầu hết senior interview.

> DSA → Databases → System Design → API Design → Java + Spring Boot → Networking cơ bản

### 📅 3 tuần — Solid preparation
Thêm các phần **🟡 Important**.

| Tuần | Focus |
|------|-------|
| 1 | 🔴 DSA + Databases + Networking |
| 2 | 🔴 System Design + API Design + Java + Spring Boot |
| 3 | 🟡 Kafka + Redis + Docker + K8s + Testing + Web Security |

### 🎯 6–8 tuần — Toàn diện
Học hết tất cả. Tham khảo thứ tự trong bảng nội dung bên dưới.

---

## Nội dung & Độ ưu tiên

> **🔴 Must** — xuất hiện trong hầu hết mọi senior interview
> **🟡 Important** — thường gặp, điểm cộng lớn
> **🟢 Bonus** — chuyên sâu, tăng cạnh tranh cho role cụ thể

---

### 1. Computer Science Foundations

| Ưu tiên | File | Nội dung |
|---------|------|---------|
| 🔴 Must | [Data Structures & Algorithms](1-Computer-Science/dsa.md) | Patterns, Backend-specific problems, LeetCode mapping |
| 🔴 Must | [Databases](1-Computer-Science/databases.md) | RDBMS, NoSQL, ACID, CAP, Indexing, Sharding, NewSQL, Time-series, Vector DB |
| 🔴 Must | [Networking](1-Computer-Science/networking.md) | HTTP/1.1/2/3, TCP/UDP, TLS, DNS, CDN, gRPC, WebSockets, SSE |
| 🟡 Important | [Concurrency](1-Computer-Science/concurrent.md) | Threading models, Virtual Threads (Java 21), Structured Concurrency |
| 🟡 Important | [Web Security](1-Computer-Science/web-security.md) | OWASP Top 10, SQL Injection, XSS, CSRF, SSRF, CORS |

---

### 2. System Design & Architecture

| Ưu tiên | File | Nội dung |
|---------|------|---------|
| 🔴 Must | [Design & Architecture](2-Design-And-Architecture/design-and-architecture.md) | Microservices, CQRS, Event Sourcing, Saga, Outbox Pattern |
| 🔴 Must | [API Design](2-Design-And-Architecture/api-design.md) | REST, Versioning, Pagination, Idempotency, Rate Limiting, GraphQL, gRPC |
| 🟡 Important | [Observability](2-Design-And-Architecture/observability.md) | Logs/Metrics/Traces, OpenTelemetry, Prometheus, Grafana, SLO |

---

### 3. Backend Technology Stack

| Ưu tiên | File | Nội dung |
|---------|------|---------|
| 🔴 Must | [Java](3-Backend-Technology-Stack/java.md) | JVM, GC, Collections, Java 17–21 (Records, Sealed, Virtual Threads) |
| 🔴 Must | [Spring Boot](3-Backend-Technology-Stack/spring.md) | IoC, MVC, Security, Spring Boot 3.x (Jakarta EE, Micrometer, GraalVM) |
| 🟡 Important | [Apache Kafka](3-Backend-Technology-Stack/kafka.md) | Architecture, Consumer Groups, Delivery Semantics, Streams |
| 🟡 Important | [Redis](3-Backend-Technology-Stack/redis.md) | Data structures, Persistence, Cluster, Sentinel, Eviction, RedLock |
| 🟢 Bonus | [Elasticsearch](3-Backend-Technology-Stack/elasticsearch.md) | Inverted index, Query DSL, Aggregations, Relevance Scoring |

---

### 4. Infrastructure

| Ưu tiên | File | Nội dung |
|---------|------|---------|
| 🟡 Important | [Docker](4-Infrastructure/docker.md) | Dockerfile, Multi-stage builds, docker-compose, Security |
| 🟡 Important | [Kubernetes](4-Infrastructure/kubernetes.md) | Architecture, Deployments, Services, Ingress, HPA, Helm, RBAC |
| 🟡 Important | [CI/CD & GitOps](4-Infrastructure/cicd-gitops.md) | GitHub Actions, ArgoCD, Deployment Strategies, Conventional Commits |
| 🟢 Bonus | [AWS Cloud](4-Infrastructure/cloud-aws.md) | EC2/ECS/EKS/Lambda, S3/RDS/DynamoDB, SQS/SNS, VPC/IAM |
| 🟢 Bonus | [Web Servers](4-Infrastructure/web-servers.md) | Nginx reverse proxy, SSL termination, Load balancing, Rate limiting |

---

### 5. Testing

| Ưu tiên | File | Nội dung |
|---------|------|---------|
| 🟡 Important | [Testing Strategy](5-Testing/testing-java.md) | Test Pyramid, JUnit 5, Mockito, Testcontainers, WireMock, Pact |

---

### 6. AI & LLM Integration

| Ưu tiên | File | Nội dung |
|---------|------|---------|
| 🟢 Bonus | [AI/LLM for Backend](6-AI-and-LLM/ai-llm.md) | RAG, Vector DB, Embeddings, AI Agents, MCP, Spring AI |

---

### Interview Preparation

| Ưu tiên | File | Nội dung |
|---------|------|---------|
| 🔴 Must | [Interview Strategy](INTERVIEW-PREPARATION.md) | System design process, STAR stories, Vietnam/SEA market |

---

## Tips ôn tập

**Nguyên tắc chung:**
- Đọc → tự giải thích lại bằng lời (Feynman technique) — không giải thích được là chưa hiểu
- Mỗi file có phần **"Interview Topics"** ở cuối — đây là những câu hay gặp nhất, ưu tiên nắm vững
- Tài liệu này là **reminder/roadmap** — khi ôn tới topic nào, tự tìm thêm code examples để practice

**Cho Senior Engineer:**
- **Depth over breadth:** interviewer expect deep knowledge ở areas bạn claim expertise
- **Trade-offs always:** mọi câu hỏi design đều trả lời "it depends" + explain trade-offs cụ thể
- **Production mindset:** mention observability, failure modes, scaling trong mọi system design
- **Honest về gaps:** admit không biết tốt hơn nói sai

**Theo loại interview:**

🖥️ **Coding/DSA heavy**
→ 🔴 DSA + Databases + Networking

🏗️ **System design heavy**
→ 🔴 Design & Architecture + API Design + Observability + Kafka + Redis

☕ **Java/Spring deep-dive**
→ 🔴 Java + Spring Boot + Concurrency + Testing

🌐 **Full-stack senior**
→ Tất cả 🔴 Must + chọn 🟡 theo JD

☁️ **Cloud/Platform role**
→ 🔴 Must + 🟢 AWS + K8s + CI/CD
