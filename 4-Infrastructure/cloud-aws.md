# AWS Cloud Essentials for Backend Engineers

> Key AWS services và patterns cho senior backend interviews

## Tại sao AWS quan trọng
- Market leader cloud (>30% share) — phổ biến nhất ở VN/SEA
- Hầu hết công ty lớn VN dùng AWS: Tyme, Grab, Shopee, Sea Group
- Interview thường hỏi: "Bạn sẽ deploy hệ thống này trên AWS như thế nào?"

---

# 1. Compute

## EC2 (Elastic Compute Cloud)
- Virtual machines — full control
- **Instance families:** General (m6i), Compute (c6i), Memory (r6i), GPU (g5)
- **Purchasing:** On-Demand, Reserved (1-3yr discount), Spot (up to 90% off, interruptible)
- **Use cases:** Legacy apps, stateful workloads, full OS control cần thiết

## ECS (Elastic Container Service)
- Managed container orchestration — simpler than K8s
- **Fargate (serverless):** không quản lý servers, pay per task
- **EC2 mode:** control over instances
- **Use cases:** teams muốn containers nhưng K8s quá phức tạp

## EKS (Elastic Kubernetes Service)
- Managed K8s control plane
- Worker nodes: EC2 managed node groups, Fargate, Karpenter
- **IRSA (IAM Roles for Service Accounts):** Pods có IAM permissions mà không cần credentials
- **Use cases:** microservices at scale, cần full K8s ecosystem

## Lambda (Serverless)
- Event-driven functions, pay per invocation
- **Triggers:** API Gateway, S3 events, SQS, EventBridge, Kinesis
- **Cold start:** JVM cold start ~1-3s → dùng SnapStart cho Java hoặc chọn runtime khác
- **Limitations:** 15min timeout, 10GB memory, 512MB /tmp
- **Use cases:** async processing, webhooks, scheduled tasks, event handlers

---

# 2. Storage

## S3 (Simple Storage Service)
- Object storage — dùng mọi nơi
- **Storage classes:** Standard → Infrequent Access → Glacier (cost optimization)
- **Features:** versioning, lifecycle policies, replication, server-side encryption
- **Presigned URLs:** cho phép client upload/download trực tiếp không qua backend
- **Use cases:** static files, backups, data lake, ML datasets, CDN origin

## EBS (Elastic Block Store)
- Block storage, attach vào EC2/EKS nodes
- Chỉ attach 1 instance tại 1 thời điểm (trừ multi-attach io2)
- **Types:** gp3 (general), io2 (high IOPS), st1 (throughput)

## EFS (Elastic File System)
- Managed NFS — nhiều instances cùng mount
- Dùng cho: shared file storage giữa containers, legacy apps

---

# 3. Databases

## RDS (Relational Database Service)
- Managed MySQL, PostgreSQL, Oracle, SQL Server, MariaDB
- **Multi-AZ:** synchronous replica trong AZ khác — automatic failover ~30-60s
- **Read Replicas:** async replication — scale reads, có thể cross-region
- **Aurora:** AWS-optimized MySQL/PostgreSQL, storage tự scale, 6 copies across 3 AZs
- **Aurora Serverless v2:** auto-scale compute — good cho variable workloads

## DynamoDB
- Serverless NoSQL, single-digit ms latency at any scale
- **Primary key:** partition key (required) + sort key (optional)
- **GSI/LSI:** Global/Local Secondary Indexes cho query patterns khác
- **DynamoDB Streams:** capture changes → trigger Lambda
- **On-Demand vs Provisioned:** auto-scale vs predict capacity
- **Use cases:** session store, gaming, IoT, cần predictable latency at scale

## ElastiCache
- Managed Redis hoặc Memcached
- **Redis:** data structures, pub/sub, persistence — richer
- **Memcached:** pure caching, simpler, multi-threaded
- **Cluster mode:** sharding across multiple nodes

---

# 4. Messaging

## SQS (Simple Queue Service)
- Managed message queue — decouple services
- **Standard queue:** at-least-once, best-effort ordering, nearly unlimited throughput
- **FIFO queue:** exactly-once, strict ordering, 3000 msg/s max
- **DLQ (Dead Letter Queue):** messages thất bại sau N lần → redirect để debug
- **Visibility timeout:** message bị "hidden" khi đang xử lý — tránh duplicate processing
- **Use cases:** async task processing, decouple microservices, buffer spikes

## SNS (Simple Notification Service)
- Pub/Sub — 1 message → nhiều subscribers
- **Fan-out pattern:** SNS topic → nhiều SQS queues
- **Subscribers:** SQS, Lambda, HTTP endpoints, email, SMS
- **Filter policies:** subscriber chỉ nhận messages match filter

## EventBridge
- Event bus — event-driven architecture
- **Rules:** route events dựa trên pattern → nhiều targets
- **Event replay:** archive & replay events
- **Use cases:** decoupled microservices, cross-account events, schedule (thay CloudWatch Events)

## Kinesis Data Streams
- Real-time data streaming (giống Kafka)
- **Shards:** đơn vị throughput (1 shard = 1MB/s write, 2MB/s read)
- Dùng khi cần replay, real-time processing, ordered within shard
- **vs SQS:** Kinesis = streaming, ordered, replay; SQS = queue, simpler

---

# 5. Networking

## VPC (Virtual Private Cloud)
- Isolated virtual network trong AWS account
- **Subnets:** Public (internet gateway) vs Private (NAT gateway)
- **Route Tables:** control traffic flow
- **Security Groups:** stateful firewall ở instance level (allow rules only)
- **NACLs:** stateless firewall ở subnet level (allow + deny)
- **NAT Gateway:** outbound internet access cho private subnets (managed, HA)

## Load Balancers
- **ALB (Application LB):** L7, HTTP/HTTPS, path/host routing, WebSocket, WAF integration
- **NLB (Network LB):** L4, TCP/UDP, ultra-low latency, static IP
- **Target Groups:** route to EC2, ECS tasks, Lambda, IP addresses

## Route 53
- DNS service + domain registration
- **Routing policies:** Simple, Weighted, Latency, Failover, Geolocation
- **Health checks:** automatic failover khi endpoint down

## CloudFront (CDN)
- Global edge network (400+ PoPs)
- **Origins:** S3, ALB, custom HTTP servers
- **Cache behaviors:** path-based caching rules
- **Lambda@Edge / CloudFront Functions:** run code at edge

---

# 6. Security & IAM

## IAM (Identity and Access Management)
- **Users:** humans (nên dùng SSO thay vì IAM users)
- **Roles:** assumed by services/resources — EC2, Lambda, EKS pods
- **Policies:** JSON documents định nghĩa permissions
- **Principle of Least Privilege:** chỉ grant permissions thực sự cần

## Secrets Manager vs Parameter Store
- **Secrets Manager:** automatic rotation, $0.40/secret/month — dùng cho DB credentials, API keys
- **Parameter Store:** free tier, không rotation tự động — dùng cho config values

## KMS (Key Management Service)
- Managed encryption keys
- Dùng để encrypt S3, RDS, Secrets Manager, EBS
- **CMK (Customer Master Key):** control key rotation, audit usage

## WAF (Web Application Firewall)
- Protect ALB/CloudFront khỏi OWASP attacks, SQL injection, XSS
- **Managed rule groups:** AWS hay third-party (CrowdStrike, Imperva)

---

# 7. Monitoring & Operations

## CloudWatch
- **Metrics:** built-in AWS metrics + custom metrics từ app
- **Logs:** centralized log storage, Log Insights để query
- **Alarms:** threshold-based alerting → SNS → PagerDuty/Slack
- **Dashboards:** visualize metrics

## X-Ray
- Distributed tracing cho AWS services
- Service map, trace analysis, latency percentiles
- **Khuyến nghị:** dùng OpenTelemetry → export sang X-Ray (không lock-in)

## AWS Config
- Record configuration changes
- Compliance rules (ví dụ: "mọi S3 bucket phải có encryption")

---

# 8. Common Architecture Patterns

## Typical Web Application
Users → CloudFront → ALB → ECS/EKS (app) → RDS (primary + read replica)
→ ElastiCache (caching layer)
→ SQS → Lambda (async processing)

## Serverless API
API Gateway → Lambda → DynamoDB / SQS (async) / S3 (files)

## Event-Driven Microservices
Service A → EventBridge → Service B
→ Service C (fan-out via SNS→SQS)
→ Archive (replay capable)

---

# 9. Well-Architected Framework (5 Pillars)

- **Operational Excellence:** IaC, observability, runbooks
- **Security:** least privilege, encryption everywhere, WAF
- **Reliability:** multi-AZ, auto-scaling, chaos engineering
- **Performance Efficiency:** right-sizing, caching, CDN
- **Cost Optimization:** reserved instances, spot, right storage class

---

# 10. Interview Topics

## Câu hỏi hay gặp
- "Làm sao achieve high availability cho database?" → Multi-AZ RDS, Aurora
- "Làm sao decouple microservices?" → SQS, SNS fan-out, EventBridge
- "S3 vs EBS vs EFS?" → Object vs Block vs File, use cases
- "Lambda cold start với Java?" → SnapStart, Provisioned Concurrency, hoặc chọn runtime khác
- "Làm sao store secrets securely?" → Secrets Manager + IAM role, không hardcode
- "Explain VPC design?" → Public/private subnets, NAT Gateway, Security Groups
