# Web Servers & Reverse Proxy

> Nginx, Apache, Caddy — cấu hình web server cho backend engineer

## Tại sao Backend Engineer cần biết
- Production deployment luôn có web server/reverse proxy phía trước app
- Interview system design hay hỏi về load balancing, SSL termination, static serving
- Troubleshooting: 502 Bad Gateway, rate limiting, caching config

---

# 1. Web Server vs Application Server vs Reverse Proxy

| Role | Làm gì | Ví dụ |
|------|--------|-------|
| **Web Server** | Serve static files (HTML, CSS, JS, images) | Nginx, Apache |
| **Application Server** | Run application code | Tomcat, Jetty, Node.js |
| **Reverse Proxy** | Forward requests to app servers, SSL termination, load balance | Nginx, HAProxy, Envoy |
| **API Gateway** | Auth, rate limiting, routing, transformation | Kong, AWS API GW, Nginx |

Thực tế: Nginx thường đảm nhiệm cả web server + reverse proxy + load balancer.

---

# 2. Nginx — Core Concepts

## Architecture
- **Event-driven, async, non-blocking** — 1 master process + N worker processes
- Worker dùng epoll/kqueue — handle thousands of connections per worker
- Rất hiệu quả với static files và I/O-bound workloads

## Key Config Structure
```
nginx.conf
├── events block     — worker connections
├── http block       — HTTP settings
│   ├── upstream     — backend server pool
│   ├── server       — virtual host
│   │   ├── listen   — port
│   │   ├── server_name — domain
│   │   └── location — URL routing rules
```

## Essential Directives (không phải code — đây là reference)
- `upstream` — define backend pool: IP:port, weight, health check
- `proxy_pass` — forward request to upstream
- `proxy_set_header` — pass headers to backend (Host, X-Real-IP, X-Forwarded-For)
- `location /api/` — route /api/* to backend; `location /` — serve static
- `try_files` — try file, then fallback (SPA routing: `try_files $uri /index.html`)
- `return 301` — permanent redirect (HTTP → HTTPS)
- `rewrite` — URL rewriting with regex

---

# 3. Reverse Proxy Pattern

## Cơ bản
```
Client → Nginx (443/80) → App Server (8080)
```
**Lợi ích:**
- SSL termination tại Nginx — app chỉ cần handle HTTP
- Hide backend details (port, number of servers)
- Static file serving không qua app
- Request buffering — protect slow backends from slow clients

## Important Headers
- `X-Forwarded-For: <client-ip>` — real client IP (Nginx thêm vào)
- `X-Real-IP: <client-ip>` — single client IP
- `X-Forwarded-Proto: https` — original protocol (vì request đến app là HTTP)
- App cần trust these headers chỉ từ trusted proxy (không từ client)

---

# 4. Load Balancing với Nginx

## Algorithms
- `round-robin` (default) — rotate qua servers theo thứ tự
- `least_conn` — gửi đến server ít active connections nhất
- `ip_hash` — same client IP → same server (sticky sessions)
- `weighted` — `weight=3` → server nhận 3x nhiều requests

## Health Checks
- Passive: detect failures khi request fail
- Active (Nginx Plus): periodic health check requests — commercial feature
- Open source alternative: upstream với `max_fails` và `fail_timeout`

---

# 5. SSL/TLS Termination

## Nginx handles TLS
- Certificate: Let's Encrypt (Certbot auto-renew) hoặc commercial cert
- Protocols: TLS 1.2 + TLS 1.3; disable TLS 1.0/1.1
- Ciphers: strong cipher suites; disable weak ones (RC4, DES, 3DES)
- HSTS header: `add_header Strict-Transport-Security "max-age=31536000"` 
- HTTP → HTTPS redirect: `return 301 https://$host$request_uri`
- SSL session cache: reuse TLS sessions — reduce handshake overhead

---

# 6. Caching với Nginx

## Static File Caching
- `expires 30d` — tell browser cache for 30 days
- `Cache-Control: public, max-age=2592000`
- Versioned assets (`/app.v2.js`) — set long cache TTL, change URL on update

## Proxy Cache (Nginx caches backend responses)
- `proxy_cache_path` — define cache storage location
- `proxy_cache_valid 200 10m` — cache 200 responses for 10 minutes
- Use for: expensive API calls, infrequently changing data
- Cache key: typically URL + selected headers

## When NOT to cache
- Authenticated requests with personalized responses
- POST/PUT/DELETE (mutating operations)
- Real-time data (stock prices, live scores)

---

# 7. Rate Limiting với Nginx

## Approaches
- `limit_req_zone` — define rate limit zone (e.g., 10 req/sec per IP)
- `limit_req` — apply zone to location
- Burst: allow short bursts, then throttle
- Return `429 Too Many Requests` on excess

## Common Use Cases
- Login endpoint: 5 req/min per IP — brute force protection
- API: 100 req/sec per API key
- File upload: 1 req/sec per IP

---

# 8. Nginx vs Apache vs Caddy

| | Nginx | Apache | Caddy |
|---|---|---|---|
| Architecture | Event-driven, async | Thread-per-connection (mpm_event improves this) | Event-driven |
| Performance | Excellent (static + proxy) | Good | Good |
| Config | nginx.conf (nginx syntax) | httpd.conf + .htaccess | Caddyfile (simple) |
| SSL/HTTPS | Manual or Certbot | Manual or Certbot | **Auto HTTPS** (Let's Encrypt built-in) |
| .htaccess | ❌ Not supported | ✅ Per-directory config | ❌ |
| Community | Very large | Very large | Growing |
| Use when | High performance, reverse proxy | Legacy apps, .htaccess needed | Simple setup, auto HTTPS |

**Caddy advantage:** Automatic HTTPS with Let's Encrypt — zero config SSL. Good cho dev/small projects.

---

# 9. HAProxy — Alternative for Load Balancing

- Dedicated load balancer — không serve static files
- Layer 4 (TCP) + Layer 7 (HTTP) load balancing
- Health checks (active, passive)
- ACLs for content-based routing
- Used by: GitHub, Stack Overflow, Reddit at large scale
- **Use when:** need advanced LB features, high connection counts, TCP LB

---

# 10. Common Production Setup

## Small Scale
```
Internet → Nginx (reverse proxy + SSL) → Spring Boot app(s)
```

## Medium Scale (K8s)
```
Internet → Cloud LB (AWS ALB) → Nginx Ingress Controller → Services → Pods
```

## Microservices
```
Internet → API Gateway (Kong/AWS API GW) → Services
                                         → Service Mesh (Istio) for internal
```

---

# 11. Troubleshooting Common Issues

| Error | Likely Cause | Check |
|-------|--------------|-------|
| 502 Bad Gateway | Backend down or not responding | App logs, `netstat` port |
| 503 Service Unavailable | All upstreams unhealthy | upstream health, `nginx -t` |
| 504 Gateway Timeout | Backend too slow | App performance, `proxy_read_timeout` |
| 404 Not Found | Location not matched | Nginx config location blocks |
| SSL error | Cert expired/invalid | `openssl s_client -connect host:443` |
| CORS error | Missing/wrong CORS headers | Browser DevTools, Nginx `add_header` config |

---

# 12. Interview Topics

## Câu hỏi hay gặp
- "Reverse proxy là gì, tại sao dùng?" → SSL termination, hide backend, load balance, static serving
- "Nginx vs Apache?" → Nginx: async event-driven, better for high concurrency; Apache: thread-based, better .htaccess support
- "Làm sao configure HTTPS?" → SSL cert (Let's Encrypt), nginx ssl config, HTTP→HTTPS redirect, HSTS
- "502 Bad Gateway là gì?" → Nginx không reach được backend — check app running, port, firewall
- "Làm sao implement rate limiting?" → Nginx `limit_req_zone` per IP/endpoint
- "X-Forwarded-For header dùng để làm gì?" → Real client IP khi có reverse proxy; app phải trust only từ proxy
