# API Design for Backend Engineers

> REST, GraphQL, gRPC — thiết kế APIs clean, scalable và maintainable

---

# 1. REST API Design

## Richardson Maturity Model
- **Level 0:** HTTP as transport tunnel (single endpoint, no HTTP semantics)
- **Level 1:** Resources (`/users`, `/orders`)
- **Level 2:** HTTP Verbs + Status Codes ← most APIs stop here
- **Level 3:** Hypermedia (HATEOAS) — responses include links to next actions

## URL Design Rules
- Nouns, not verbs: `/users` not `/getUsers`
- Plural resources: `/users` not `/user`
- Lowercase with hyphens: `/payment-methods` not `/paymentMethods`
- Max 2 levels deep: `/orders/{orderId}/items` OK; go deeper → flatten with query params

Good patterns:
- GET /users — list users
- POST /users — create user
- GET /users/{id} — get user
- PUT /users/{id} — full replace
- PATCH /users/{id} — partial update
- DELETE /users/{id} — delete

Avoid: verb in URL (`/getUser`), redundant verb (`/users/create`), nesting > 2 levels

## HTTP Methods & Idempotency
| Method | Idempotent | Safe | Dùng cho |
|--------|-----------|------|----------|
| GET    | ✅        | ✅   | Read     |
| PUT    | ✅        | ❌   | Full replace |
| DELETE | ✅        | ❌   | Delete   |
| POST   | ❌        | ❌   | Create   |
| PATCH  | ❌        | ❌   | Partial update |

## HTTP Status Codes
| Code | Meaning |
|------|---------|
| 200 OK | success với response body |
| 201 Created | resource created → thêm Location header |
| 204 No Content | success, no body (DELETE, PUT) |
| 400 Bad Request | invalid input, validation error |
| 401 Unauthorized | not authenticated |
| 403 Forbidden | authenticated but no permission |
| 404 Not Found | resource không tồn tại |
| 409 Conflict | duplicate, stale data |
| 422 Unprocessable Entity | semantically invalid (business rule violation) |
| 429 Too Many Requests | rate limited |
| 500 Internal Server Error | unexpected server error |
| 503 Service Unavailable | server down or overloaded |

---

# 2. API Versioning

## Strategies
| Strategy | Example | When to Use |
|----------|---------|-------------|
| URL path | `GET /api/v1/users` | Most common, easy to test, cache-friendly |
| Header | `Accept: application/vnd.company.v2+json` | Cleaner URLs |
| Query param | `GET /api/users?version=2` | Not recommended |

**URL path** most popular: visible in logs, easy to test in browser/curl, cache-friendly

## Versioning Principles
- Only version on **breaking changes**: removing fields, changing response format, changing behavior
- **Non-breaking changes**: adding optional fields, new endpoints → no new version needed
- Support old version ≥ 6–12 months after deprecation announcement
- Signal deprecation: `Deprecation` header + `Sunset` date in responses

---

# 3. Pagination

## Offset-based (traditional)
- Request: `GET /users?page=2&size=20`
- Response: `{ data: [...], total: 1000, page: 2, totalPages: 50 }`
- **Pros:** random access, sort by any field
- **Cons:** slow for large offsets (DB scans all rows up to offset), inconsistent if data changes

## Cursor-based (recommended for large datasets)
- First page: `GET /users?limit=20`
- Next pages: `GET /users?limit=20&cursor=<opaque-cursor>`
- Response: `{ data: [...], nextCursor: "...", hasMore: true }`
- **Pros:** O(1) performance with index, consistent even as data changes
- **Cons:** no random access; cursor is typically base64-encoded last item ID + sort key

## Keyset Pagination (for sorted data)
- Request: `GET /orders?after_created_at=2026-01-01T00:00:00Z&limit=20`
- **Pros:** fast (uses index), consistent
- **Cons:** forward navigation only

---

# 4. Error Handling

## RFC 9457 — Problem Details (Spring Boot 3.x built-in)
Standard JSON error format with fields:
- `type` — URI identifying the error class (e.g., `https://api.company.com/errors/insufficient-funds`)
- `title` — human-readable summary
- `status` — HTTP status code (400, 404, etc.)
- `detail` — specific message for this occurrence
- `instance` — URI of the specific request (e.g., `/payments/order-789`)
- Custom extensions: `traceId`, `timestamp`, `violations` (for validation)

Enable in Spring Boot 3: `spring.mvc.problemdetails.enabled=true`

## Consistent Error Response Principles
- Always return same format for all errors
- Include `traceId` so support team can debug
- Never expose stack traces in production
- `@ControllerAdvice` for global exception handling in Spring

---

# 5. Idempotency

## Problem
Client sends request, doesn't receive response (network timeout) → retries → duplicate operation (duplicate payment!)

## Idempotency Key Pattern
1. Client includes `Idempotency-Key: <uuid>` header on request
2. Server checks Redis: key already exists?
   - Yes → return cached response (no reprocessing)
   - No → process, store `{key: response}` in Redis with TTL, return response
3. TTL: 24h for payments, adjust per use case

**Critical for:** payments, order creation, any state-changing operation the client might retry

**Implementations note:** consumers of your API must generate unique key per logical operation (not per retry)

---

# 6. Rate Limiting

## Algorithms
- **Fixed Window:** count requests in fixed time window (1000 req/min). Simple but burst possible at window boundary.
- **Sliding Window:** window moves with time — more accurate, prevents boundary burst
- **Token Bucket:** bucket refills at fixed rate, allows bursts. Most flexible.
- **Leaky Bucket:** queue requests, process at fixed rate — smooths traffic, no bursts

## Redis Implementation Patterns
- Token Bucket: store token count in Redis key with TTL
- Sliding Window Log: sorted set with timestamps, remove old entries, count remaining
- Sliding Window Counter: two adjacent fixed windows weighted by position in current window

## Response Headers
- `X-RateLimit-Limit: 1000` — max requests allowed
- `X-RateLimit-Remaining: 850` — requests remaining in window
- `X-RateLimit-Reset: 1699999200` — Unix timestamp when window resets
- `Retry-After: 60` — seconds to wait (when 429 returned)

---

# 7. API Security

## Authentication Flows

### JWT Bearer Token
- Login → returns `{ accessToken, refreshToken }`
- API calls: `Authorization: Bearer <accessToken>`
- Refresh: POST to `/auth/refresh` with refreshToken → new accessToken
- Access token TTL: 15–60 minutes; Refresh token TTL: 7–30 days

### OAuth2 Flows
- **Authorization Code + PKCE:** web/mobile apps — user login via identity provider
- **Client Credentials:** service-to-service (no user involved)
- **Device Code:** TV/CLI apps (no browser redirect possible)

## API Key Management
- Hash before storing — never store plaintext keys
- Include key ID to allow targeted revocation
- Rate limit per API key

## Security Headers
- `Strict-Transport-Security: max-age=31536000; includeSubDomains` — force HTTPS
- `X-Content-Type-Options: nosniff` — prevent MIME sniffing
- `X-Frame-Options: DENY` — prevent clickjacking
- `Content-Security-Policy: default-src 'self'` — restrict resource loading

---

# 8. OpenAPI / Swagger

## Code-First vs Design-First
- **Design-First:** write OpenAPI spec first → generate stubs → contracts clear before coding
- **Code-First:** annotate code (SpringDoc) → generate spec automatically
- **Recommendation:** Design-First for important API contracts (consumer-driven APIs)

## OpenAPI 3.0 Key Elements
- `paths` — endpoints with methods, request/response schemas
- `components` — reusable schemas, responses, parameters, security schemes
- `security` — global or per-endpoint security requirements (bearerAuth, apiKey)
- `requestBody` — required/optional body with content type + schema
- Inline docs: `summary`, `description`, `example`, `deprecated`

---

# 9. GraphQL

## Khi nào dùng GraphQL
✅ Client needs flexible queries — mobile vs web needs different data shapes
✅ Aggregating multiple backend services into one endpoint
✅ Rapid product iteration, schema evolution
❌ Simple CRUD APIs (REST simpler)
❌ Real-time streaming (use WebSocket/SSE)
❌ File upload/download

## Core Operations
- **Query** — read data, can specify exactly which fields
- **Mutation** — create/update/delete data
- **Subscription** — real-time event stream

## N+1 Problem → DataLoader
Without batching: 1 query for users + N queries for each user's orders
With DataLoader: batch + cache → 1 query for all users' orders simultaneously

---

# 10. gRPC

> Protocol details (HTTP/2, 4 streaming modes) → see [networking.md](../1-Computer-Science/networking.md)

## Khi nào dùng gRPC
✅ Internal service-to-service communication
✅ High throughput, low latency required
✅ Streaming needed (server push, bidirectional)
✅ Strongly-typed contracts with Protobuf
❌ Public APIs — not browser-friendly without grpc-web proxy
❌ Teams not willing to manage Protobuf complexity

## gRPC vs REST
| | REST | gRPC |
|---|------|------|
| Protocol | HTTP/1.1 or HTTP/2 | HTTP/2 required |
| Format | JSON (verbose, human-readable) | Protobuf (binary, 3–10x smaller) |
| Browser | ✅ Native | ❌ Needs grpc-web proxy |
| Streaming | Limited (SSE, WebSocket) | Built-in (4 modes) |
| Code gen | Optional (OpenAPI) | Required (Protobuf .proto) |
| Debugging | Easy (readable) | Harder (binary) |

## Protocol Buffer (Protobuf) Basics
- Define `.proto` file: services + messages with typed fields + field numbers
- Field numbers must never change once published — backward/forward compatibility
- Adding new fields (new numbers) is safe; removing fields breaks compatibility
- Code generated: clients, servers, and message types in target language

---

# 11. API Design Checklist

## REST API
- [ ] Nouns in URLs, HTTP methods for actions
- [ ] Correct status codes (201 Created with Location, 204 for DELETE)
- [ ] Consistent error format (RFC 9457)
- [ ] Pagination (cursor-based for large datasets)
- [ ] Versioning strategy decided
- [ ] Idempotency key for state-changing operations
- [ ] Rate limiting with proper headers
- [ ] OpenAPI spec for documentation

---

# 12. Interview Topics

## Câu hỏi hay gặp
- "REST vs GraphQL vs gRPC — khi nào dùng cái nào?" → REST for public APIs; gRPC for internal high-throughput; GraphQL for flexible client queries
- "Idempotency key làm việc như thế nào?" → Redis cache with TTL; same key returns same response
- "Offset vs cursor pagination?" → Cursor for large/real-time data; offset for random access
- "Làm sao version API without breaking clients?" → URL path versioning; non-breaking changes don't need new version
- "Rate limiting algorithms?" → Token bucket, sliding window — trade-offs: burst tolerance, accuracy, complexity
- "JWT vs session?" → JWT: stateless, easy to scale; Session: stateful, easy to revoke
