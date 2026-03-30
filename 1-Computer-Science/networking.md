# Networking for Backend Engineers

> Essential networking knowledge for system design and backend development

## 🎯 Why Networking Matters for Backend Engineers
- **System Design interviews:** Load balancing, CDN, API design, microservices communication
- **Performance optimization:** Understanding latency, throughput, caching strategies
- **Troubleshooting:** Debug production issues, analyze bottlenecks
- **Architecture decisions:** Choose protocols, design resilient systems

---

# 1. Network Fundamentals

## 1.1 Core Concepts
**IP & Addressing:**
- IPv4/IPv6, Private/Public IP ranges (10.x, 192.168.x, 172.16-31.x)
- Subnetting, CIDR notation (/24, /16)
- DNS resolution (A, AAAA, CNAME, MX records)
- NAT/PAT for private network access

**Protocol Stack:**
- **TCP:** Reliable, ordered, handshake (3-way), flow control, congestion control
- **UDP:** Fast, unreliable, stateless - good for real-time apps (gaming, video)
- **HTTP/HTTPS:** Application layer, stateless, request-response

**Key insight for interviews:** Choose UDP for real-time, TCP for reliability

---

# 2. Application Layer Protocols & APIs

## 2.1 HTTP/HTTPS Essentials
**HTTP Basics:**
- **Methods:** GET (idempotent), POST, PUT (idempotent), PATCH, DELETE (idempotent)
- **Status codes:** 2xx (success), 4xx (client error), 5xx (server error)
- **Headers:** Authorization, Content-Type, Cache-Control, CORS headers

**HTTPS & Security:**
- TLS handshake, certificates, SNI (Server Name Indication)
- CORS: preflight requests, credentials handling
- Security headers: HSTS, CSP, X-Frame-Options

**Performance:**
- HTTP/2: multiplexing, server push, binary protocol
- Keep-alive connections, connection pooling

## 2.2 API Design Patterns
**REST fundamentals:**
- **Stateless:** Each request contains all needed information
- **Idempotency:** GET, PUT, DELETE safe to retry
- **Resource-based URLs:** `/users/123` not `/getUser?id=123`
- **Versioning:** URL path vs headers vs query params

**Modern API patterns:**
- **GraphQL:** Query language, single endpoint, client-specified data
- **gRPC:** HTTP/2, Protocol Buffers, streaming, strong typing
- **WebSocket:** Full-duplex, real-time communication, persistent connection

**Interview relevance:** Choose HTTP for CRUD, WebSocket for real-time, gRPC for internal services

---

# 3. Scalability & Performance Patterns

## 3.1 Load Balancing & Proxy Patterns
**Load Balancer Types:**
- **L4 (Transport layer):** TCP/UDP level, faster, less features
- **L7 (Application layer):** HTTP level, content-based routing, SSL termination

**Algorithms:**
- **Round Robin:** Simple, even distribution
- **Least Connections:** Better for long-lived connections
- **Weighted:** Handle different server capacities
- **Health Checks:** Remove failed servers from rotation

**Proxy patterns:**
- **Reverse Proxy:** Server-side, SSL termination, caching, compression
- **Forward Proxy:** Client-side, content filtering, anonymity
- **API Gateway:** Authentication, rate limiting, request/response transformation

## 3.2 Caching & Content Delivery
**CDN (Content Delivery Network):**
- **Edge caching:** Serve content from geographically closer servers
- **Cache strategies:** TTL, cache invalidation, cache warming
- **Use cases:** Static assets, API responses, database query results

**Cache Patterns:**
- **Cache-aside:** Application manages cache
- **Write-through:** Write to cache and database simultaneously
- **Write-behind:** Write to cache first, database later
- **Cache levels:** Browser → CDN → Load balancer → Application → Database

## 3.3 Performance Optimization
**Latency reduction:**
- **Connection reuse:** HTTP keep-alive, connection pooling
- **Compression:** gzip, brotli for text content
- **Protocol optimization:** HTTP/2 multiplexing, TLS session resumption

**Scalability patterns:**
- **Horizontal scaling:** Add more servers (stateless design required)
- **Vertical scaling:** Bigger servers (easier but limited)
- **Async processing:** Message queues, event-driven architecture
- **Circuit breaker:** Fail fast, prevent cascading failures

**Interview insight:** Always discuss caching strategy and failure handling in system design

---

# 4. Security & Authentication

## 4.1 Network Security Fundamentals
**Transport Security:**
- **TLS/SSL:** Encryption in transit, certificate validation, SNI
- **HTTPS everywhere:** Protect data, SEO benefits, browser requirements
- **Certificate management:** Let's Encrypt, wildcard certs, rotation

**Authentication vs Authorization:**
- **Authentication:** Who are you? (login, JWT, API keys)
- **Authorization:** What can you do? (roles, permissions, scopes)

## 4.2 Common Security Patterns
**Token-based Authentication:**
- **JWT:** Self-contained, stateless, signature validation
- **OAuth2:** Authorization framework, scopes, refresh tokens
- **API Keys:** Simple but less secure, rate limiting, rotation

**Web Security:**
- **CORS:** Same-origin policy, preflight requests, credentials
- **Security Headers:** HSTS, CSP, X-Frame-Options, X-XSS-Protection
- **Common attacks:** XSS, CSRF, SQL injection, DDoS mitigation

**Interview insight:** Always discuss authentication flow and security headers in API design

---

# 5. Modern Infrastructure & Cloud

## 5.1 Container & Microservices Networking
**Container Networking:**
- **Docker networking:** Bridge, host, overlay networks
- **Kubernetes networking:** Pods, Services, Ingress controllers
- **Service mesh:** Istio, Linkerd - traffic management, observability, security

**Microservices Communication:**
- **Service Discovery:** DNS-based, registry-based (Consul, Eureka)
- **Load balancing:** Client-side vs server-side
- **Circuit breaker:** Prevent cascading failures, fallback strategies

## 5.2 Cloud Networking Essentials
**Virtual Private Cloud (VPC):**
- **Subnets:** Public (internet access) vs private (internal only)
- **Route tables:** Control traffic flow between subnets
- **Security groups:** Instance-level firewall rules
- **NAT Gateway:** Outbound internet access for private subnets

**Load Balancers in Cloud:**
- **Application Load Balancer (L7):** HTTP/HTTPS, host/path-based routing
- **Network Load Balancer (L4):** TCP/UDP, ultra-high performance
- **API Gateway:** Managed service, authentication, rate limiting, caching

**Interview relevance:** Essential for system design questions involving scalable web applications

---

# 6. Observability & Troubleshooting

## 6.1 Network Debugging Tools
**Command-line essentials:**
- **ping:** Basic connectivity, latency measurement
- **traceroute:** Network path analysis, identify bottlenecks
- **nslookup/dig:** DNS resolution debugging
- **curl/wget:** HTTP testing, header inspection
- **netstat/ss:** Connection monitoring, port usage

**Advanced debugging:**
- **tcpdump/Wireshark:** Packet capture and analysis
- **Browser DevTools:** Network tab, performance analysis
- **Load testing:** ab, JMeter, wrk for performance testing

## 6.2 Production Monitoring
**Key metrics:**
- **Latency:** P50, P95, P99 response times
- **Throughput:** Requests per second, bandwidth usage
- **Error rates:** 4xx, 5xx status codes
- **Connection metrics:** Pool usage, timeout rates

**Observability:**
- **Logging:** Structured logs, correlation IDs, log aggregation
- **Metrics:** Time-series data, dashboards (Grafana)
- **Tracing:** Distributed tracing, request flow analysis

**Interview tip:** Always mention monitoring and alerting when designing systems

---

# 7. Classic Interview Case Study

## "What happens when you type `www.google.com`?"

**Complete flow with senior-level insights:**

1. **DNS Resolution**
   - Browser cache → OS cache → Router cache → ISP DNS → Root servers
   - **Insight:** DNS caching reduces latency, TTL controls cache duration

2. **TCP Connection**
   - 3-way handshake: SYN → SYN-ACK → ACK
   - **Insight:** Connection pooling reuses connections, reduces handshake overhead

3. **TLS Handshake**
   - Certificate verification, cipher negotiation, key exchange
   - **Insight:** TLS session resumption speeds up subsequent connections

4. **HTTP Request**
   - Headers, cookies, content negotiation
   - **Insight:** HTTP/2 multiplexing allows multiple requests per connection

5. **Load Balancing & CDN**
   - Edge servers, geographic routing, cache hits/misses
   - **Insight:** CDN reduces server load and improves global performance

6. **Server Processing & Response**
   - Application logic, database queries, response generation
   - **Insight:** Caching, database optimization, async processing improve performance

**Senior discussion points:**
- Security considerations at each step
- Performance optimizations
- Failure scenarios and resilience
- Monitoring and observability

---

# 📚 Essential Resources

## Must-Read Materials
- [Computer Networking: A Top-Down Approach](pdf/Computer-Networking-A-Top-Down-Approach-8th-Edition.pdf) - Comprehensive networking fundamentals

## Online Resources
- [High Performance Browser Networking](https://hpbn.co/) - Web performance optimization
- [HTTP/2 Explained](https://http2-explained.haxx.se/) - Modern HTTP protocol
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/) - Cloud networking best practices

## Quick Reference
**For System Design Interviews:**
- Choose protocols based on requirements (HTTP vs WebSocket vs gRPC)
- Always discuss caching strategy and CDN usage
- Consider load balancing and failure scenarios
- Mention monitoring and observability

**For Technical Deep-dives:**
- Know TLS handshake process and performance implications
- Understand HTTP/2 benefits and when to use it
- Be familiar with cloud networking concepts (VPC, security groups)
- Explain debugging approach for network issues
