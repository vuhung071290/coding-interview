# Networking for Backend Engineers

> Essential networking knowledge for system design and backend development

# 1. Network Fundamentals

## IP & Addressing
- IPv4/IPv6, Private/Public IP ranges (10.x, 192.168.x, 172.16-31.x)
- Subnetting, CIDR notation (/24, /16)
- DNS resolution — record types: A (IPv4), AAAA (IPv6), CNAME (alias), MX (mail)
- NAT/PAT for private network access

## TCP vs UDP
| | TCP | UDP |
|---|---|---|
| Reliability | Guaranteed (ACK, retransmit) | Best-effort |
| Order | Ordered | No ordering |
| Connection | 3-way handshake | Stateless |
| Overhead | Higher | Lower |
| Use when | HTTP, DB, file transfer | Real-time (video, gaming, DNS) |

## HTTP Versions

| Version | Transport | Key Feature |
|---|---|---|
| HTTP/1.1 | TCP + TLS | Keep-alive, one request at a time per connection |
| HTTP/2 | TCP + TLS | Multiplexing (multiple streams, 1 connection), binary, header compression, server push |
| HTTP/3 | QUIC (UDP) | Stream-level reliability, 0-RTT resumption, connection migration |

**HTTP/3 / QUIC key advantages:**
- Runs over UDP — built-in TLS 1.3, no TCP-level head-of-line blocking
- **Connection migration:** IP thay đổi (wifi → 4G) → connection không bị drop (ID-based)
- **0-RTT:** reconnect không cần full handshake
- Dùng khi: mobile clients, high-latency networks, CDN (Cloudflare, Google đã support)

---

# 2. HTTP Essentials

## Methods & Idempotency
| Method | Idempotent | Safe | Dùng cho |
|--------|-----------|------|----------|
| GET    | ✅        | ✅   | Read |
| PUT    | ✅        | ❌   | Full replace |
| DELETE | ✅        | ❌   | Delete |
| POST   | ❌        | ❌   | Create |
| PATCH  | ❌        | ❌   | Partial update |

## Status Codes (key ones)
| Code | Meaning |
|------|---------|
| 200 OK | success |
| 201 Created | resource created |
| 204 No Content | success, no body |
| 301/302 | Redirect |
| 400 Bad Request | invalid input |
| 401 Unauthorized | not authenticated |
| 403 Forbidden | no permission |
| 404 Not Found | resource không tồn tại |
| 409 Conflict | duplicate/stale data |
| 429 Too Many Requests | rate limited |
| 500 Internal Server Error | server crash |
| 503 Service Unavailable | overloaded/down |

## TLS/HTTPS
- TLS handshake: certificate verification → cipher negotiation → key exchange
- TLS session resumption: giảm latency cho repeated connections
- HSTS, cert rotation, SNI (Server Name Indication)
- Security headers: HSTS, CSP, X-Frame-Options, X-Content-Type-Options

---

# 3. gRPC — Protocol Overview

> Chi tiết REST vs gRPC vs GraphQL khi nào dùng → xem [API Design](../2-Design-And-Architecture/api-design.md)

## Basics
- Protocol: **HTTP/2** + **Protocol Buffers** (binary serialization)
- Strongly-typed contracts (`.proto` file), code generation required
- **3–10x smaller** payload vs JSON

## 4 Communication Modes
| Mode | Description |
|------|-------------|
| Unary | 1 request → 1 response (giống REST) |
| Server Streaming | 1 request → stream of responses |
| Client Streaming | stream of requests → 1 response |
| Bidirectional | stream ↔ stream simultaneously |

## gRPC vs REST
| | REST/JSON | gRPC/Protobuf |
|---|---|---|
| Payload | Verbose (text) | Compact (binary) |
| Schema | Optional (OpenAPI) | Required (Protobuf) |
| Browser | Native | Cần grpc-web proxy |
| Streaming | Workarounds | Built-in (4 modes) |
| Debugging | Easy (readable) | Harder (binary) |

**Khi nào dùng gRPC:** Internal service-to-service, high throughput, low latency, streaming
**Khi nào dùng REST:** Public APIs, browser clients, simplicity

**Protobuf compatibility:** thêm fields (new field numbers) OK — không xóa/đổi field numbers

---

# 4. Load Balancing & Proxy

## Load Balancer Types
- **L4 (Transport):** TCP/UDP level — fast, less features
- **L7 (Application):** HTTP level — content-based routing, SSL termination, host/path routing

## Algorithms
- **Round Robin:** simple, even distribution
- **Least Connections:** better for long-lived connections
- **Weighted:** handle different server capacities
- **IP Hash:** sticky sessions (same client → same server)

## Proxy Patterns
- **Reverse Proxy:** server-side, SSL termination, caching, compression (Nginx, HAProxy)
- **API Gateway:** auth, rate limiting, request transformation (Kong, AWS API GW)
- **Service Mesh:** Istio, Linkerd — traffic management, mTLS, observability between services

---

# 5. CDN & Caching

- **CDN:** edge servers serve content từ gần user nhất — reduces latency, server load
- **Cache-Control headers:** `max-age`, `no-cache`, `no-store`, `s-maxage`
- **Cache invalidation:** TTL-based, event-triggered, cache busting (versioned URLs)
- **Cache levels:** Browser → CDN → Load Balancer → App → Database

---

# 6. Cloud Networking Essentials

## VPC Concepts
- **Subnets:** Public (internet access) vs Private (internal only)
- **Route tables:** control traffic flow between subnets
- **Security groups:** instance-level stateful firewall rules
- **NAT Gateway:** outbound internet for private subnets
- **VPC Peering / Transit Gateway:** connect multiple VPCs

## Microservices Networking
- **Service Discovery:** DNS-based (K8s services), registry-based (Consul, Eureka)
- **Circuit breaker:** prevent cascading failures (Resilience4j, Envoy)

---

# 7. WebSockets & Real-Time Communication

## WebSocket
- **Full-duplex** persistent connection — cả client và server có thể gửi bất kỳ lúc nào
- Handshake: HTTP Upgrade request → server responds 101 Switching Protocols → WS connection
- Protocol: `ws://` (plain) hoặc `wss://` (over TLS)
- **Use cases:** chat, live notifications, real-time dashboards, collaborative editing, live scores

## WebSocket vs HTTP Polling vs SSE

| | HTTP Polling | Long Polling | SSE | WebSocket |
|---|---|---|---|---|
| Direction | Client → Server | Client → Server | Server → Client only | Bidirectional |
| Connection | New per request | Held open until event | Persistent (one-way) | Persistent (two-way) |
| Overhead | High (repeated requests) | Medium | Low | Low |
| Browser support | ✅ | ✅ | ✅ | ✅ |
| Use when | Simple, infrequent updates | Moderate frequency | Server push only | Bidirectional real-time |

## Server-Sent Events (SSE)
- **One-way:** Server → Client only (không cần bidirectional)
- Native browser `EventSource` API — auto-reconnect built-in
- **Use cases:** live feed, progress updates, notifications, log streaming
- Text-based (not binary) — simpler than WebSocket
- Works over HTTP/2 (multiplexed) — scales better than HTTP/1.1 SSE

## Spring Boot Support
- **WebSocket:** `spring-boot-starter-websocket` + SockJS + STOMP (messaging protocol over WS)
- **SSE:** `SseEmitter` hoặc reactive `Flux<ServerSentEvent>` với WebFlux

## Scaling Real-Time Connections
- WebSocket connections are stateful → sticky sessions hoặc pub/sub backend
- **Pattern:** WS clients connect to any server → servers communicate via Redis Pub/Sub or Kafka → fan out to connected clients
- Load balancer: Nginx `proxy_http_version 1.1` + `Upgrade` header passthrough for WS

---

# 8. Classic Interview: "What happens when you type google.com?"

1. **DNS Resolution** → Browser cache → OS → ISP → Root → Authoritative
2. **TCP 3-way handshake** → SYN → SYN-ACK → ACK
3. **TLS handshake** → cert verify → key exchange → session established
4. **HTTP Request** → headers, cookies, content negotiation
5. **Load Balancer / CDN** → edge cache hit? → origin server
6. **Server processing** → app logic → DB → response

**Discussion points:** connection pooling, HTTP/2 multiplexing, CDN cache hit rates, TLS session resumption

---

# 9. Debugging Tools (reference)
- `ping` — basic connectivity, latency
- `traceroute` / `tracert` — network path, identify bottlenecks
- `nslookup` / `dig` — DNS debugging
- `curl -v` — HTTP testing with headers
- `netstat` / `ss` — connection monitoring, port usage
- Browser DevTools Network tab — HTTP waterfall analysis

---

# Quick Reference
**For System Design Interviews:**
- Choose protocol based on requirements (HTTP vs WebSocket vs gRPC)
- Always discuss caching strategy and CDN
- Consider load balancing, failure scenarios, circuit breakers
- Mention Observability → xem [observability.md](../2-Design-And-Architecture/observability.md)

**For Technical Deep-dives:**
- HTTP/2 multiplexing vs HTTP/3 QUIC
- TLS handshake và session resumption
- Cloud VPC, security groups, NAT
