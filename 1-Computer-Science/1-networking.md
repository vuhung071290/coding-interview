# Networking

---

# 1. Networking Fundamentals & IP Addressing

- IPv4/IPv6, Private/Public, Subnetting, CIDR
- Loopback, Broadcast, Multicast
- DNS, DHCP
- NAT/PAT, Port Forwarding
- Routing: Static/Dynamic (OSPF/BGP), Routing Table

---

# 2. OSI Model & TCP/IP Model

- OSI 7 tầng ↔ TCP/IP 4 tầng
- Encapsulation / Decapsulation
- TCP: reliable, handshake, congestion/flow control
- UDP: lightweight, không đảm bảo, tốc độ cao

---

# 3. HTTP, REST API & Real-time Communication

## HTTP
- Methods, Status Codes, Headers
- Cookies, Tokens
- HTTPS, CORS, Caching
- HTTP/2, Persistent Connections

## REST API
- Stateless
- Idempotency
- Versioning

## Real-time
- WebSocket
- Server-Sent Events
- Long Polling
- gRPC

---

# 4. Proxy, CDN & Load Balancing

## Proxy
- Forward Proxy
- Reverse Proxy
- API Gateway

## CDN
- Edge caching
- Giảm latency, tăng throughput

## Load Balancing
- L4 vs L7
- Round Robin, Least Connections
- Sticky Session
- Health Check

---

# 5. Network & Web Security

## Transport & Network Security
- TLS/SSL handshake, certificates
- HTTPS
- VPN
- Firewall: Stateless / Stateful
- IDS / IPS

## Auth & Cryptography
- Authentication vs Authorization
- Tokens vs Sessions, JWT
- Symmetric / Asymmetric Encryption
- Public/Private Keys, Certificates
- Hashing, MAC

## Web Security
- Secure Headers
- CORS, CSP
- XSS, CSRF, SQL Injection

---

# 6. Performance Optimization & Scalability

## Performance
- Latency, Bandwidth, Throughput
- Head-of-Line Blocking
- gzip, brotli
- HTTP keep-alive, HTTP/2
- TLS resumption

## Scalability
- Stateless vs Stateful
- Horizontal vs Vertical scaling
- Retry, Backoff, Circuit Breaker
- Rate Limiting
- Caching: CDN, Redis

---

# 7. Cloud Networking & System Design

## Cloud Networking
- VPC, Subnet, Route Table
- NAT Gateway, Internet Gateway
- Security Group vs NACL
- PrivateLink, VPC Peering
- Load Balancers: ELB, ALB, NLB
- Service Mesh: Istio, Linkerd

## System Design Use Cases
- Chat system: WebSocket, Pub/Sub
- Streaming: UDP/TCP, CDN
- API Gateway
- NAT Traversal (WebRTC)
- Firewall Rules, Access Control
- VPC Network Design

---

# 8. Network Monitoring & Debugging

## CLI Tools
- ping, traceroute
- nslookup, dig
- netstat, lsof
- telnet, nc, curl, wget
- tcpdump, Wireshark

## Monitoring & Observability
- DevTools Network Tab
- Grafana, Nagios
- Logging, Metrics, Tracing
- Bottleneck analysis

---

# 9. Case Study — What Happens When You Type `www.google.com`?

1. DNS Resolution
2. TCP Handshake
3. TLS Handshake
4. HTTP Request
5. CDN/Proxy xử lý → Server trả về
6. Browser render

---

# 10. Reference Materials

## Books
- [Computer Networking: A Top-Down Approach (8th Edition)](./pdf/Computer-Networking-A-Top-Down-Approach-8th-Edition.pdf)
