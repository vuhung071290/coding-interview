# Web Security for Backend Engineers

> OWASP Top 10, common vulnerabilities, defense strategies — phần không thể thiếu trong senior interview

## Tại sao Web Security quan trọng
- Senior interview thường hỏi "làm sao bảo vệ API của bạn?"
- Security là cross-cutting concern — ảnh hưởng mọi layer
- Data breach = trust loss + legal liability + financial damage

---

## 1. OWASP Top 10 (2021)

| # | Vulnerability | Mô tả ngắn |
|---|---------------|------------|
| A01 | Broken Access Control | User truy cập resource không được phép |
| A02 | Cryptographic Failures | Dùng weak crypto, store plaintext passwords |
| A03 | Injection (SQL, LDAP, OS) | Untrusted data được execute như command |
| A04 | Insecure Design | Thiếu security design từ đầu |
| A05 | Security Misconfiguration | Default credentials, unnecessary features enabled |
| A06 | Vulnerable Components | Dùng thư viện có known CVEs |
| A07 | Auth & Session Failures | Weak passwords, session fixation |
| A08 | Software Integrity Failures | Unverified updates, CI/CD without integrity checks |
| A09 | Logging & Monitoring Failures | Không detect/alert attacks |
| A10 | SSRF | Server bị trick để request internal resources |

---

## 2. Injection Attacks

### SQL Injection
- **Vấn đề:** User input concatenated trực tiếp vào SQL query
- **Ví dụ attack:** `username = "admin'--"` → bypass password check
- **Defense:**
  - Parameterized queries / Prepared statements — không dùng string concat
  - ORM (JPA/Hibernate) — tự dùng parameterized
  - Input validation (whitelist, không blacklist)
  - Least privilege DB user — app user không có DROP/ALTER

### NoSQL Injection
- MongoDB: JSON injection với `$where`, `$gt`, `$regex` operators
- Defense: validate/sanitize input before passing to DB, use typed ODM

### Command Injection
- Untrusted input passed to OS commands (`Runtime.exec()`, shell scripts)
- Defense: avoid shell commands; nếu cần → whitelist args, escape input

---

## 3. Cross-Site Scripting (XSS)

### Types
- **Stored XSS:** malicious script lưu vào DB, execute khi user load page
- **Reflected XSS:** script trong URL/request được reflect trong response
- **DOM-based:** script manipulates DOM client-side

### Defense
- **Output encoding:** escape HTML entities (`<` → `&lt;`) trước khi render
- **Content Security Policy (CSP):** header restricts script sources
- **HttpOnly cookies:** JS không thể read session cookies
- **Input validation:** whitelist allowed characters/formats

### Backend responsibility
- Encode output before returning in API responses (nếu HTML)
- CSP headers trong API Gateway / server config
- Sanitize rich text input (HTML) nếu cho phép user nhập formatted text

---

## 4. Cross-Site Request Forgery (CSRF)

### Attack Flow
Victim logged in → visits malicious site → malicious site sends request to victim's bank using victim's cookies → bank thinks it's legitimate

### Defense
- **CSRF Token:** random token trong form/header — server validate token khớp session
- **SameSite cookie:** `SameSite=Strict` hoặc `Lax` — browser không gửi cookie cross-site
- **Origin/Referer header validation:** check request origin
- **REST APIs với JSON body:** mặc định protected (browsers không send JSON cross-origin without CORS)
- **CORS properly configured** là related defense (xem section 7)

---

## 5. Broken Access Control

### Common Vulnerabilities
- **IDOR (Insecure Direct Object Reference):** `/api/orders/1234` — user truy cập order của người khác bằng cách đổi ID
- **Privilege escalation:** user thường truy cập admin endpoint
- **Path traversal:** `../../../etc/passwd` trong file download API
- **Missing function-level auth:** endpoint tồn tại nhưng không check permission

### Defense
- **Authorize every request:** không assume "nếu user logged in, họ có quyền"
- **Resource ownership check:** `order.getUserId().equals(currentUserId)`
- **RBAC (Role-Based Access Control):** Spring Security `@PreAuthorize("hasRole('ADMIN')")`
- **Deny by default:** explicit grant, không implicit allow
- **Audit log:** log ai truy cập gì, khi nào

---

## 6. Authentication & Session Security

### Password Security
- **Never store plaintext** — hash với BCrypt, Argon2, PBKDF2 (không MD5/SHA1)
- **Salt:** auto-included trong BCrypt — prevents rainbow table attacks
- **Pepper:** application-level secret added before hashing (extra layer)
- Minimum password policy + account lockout sau N failed attempts

### Session Management
- **Session fixation:** regenerate session ID after login
- **Session timeout:** idle timeout (30 min) + absolute timeout (8h)
- **Secure cookie flags:** `HttpOnly`, `Secure`, `SameSite`
- **JWT:** short-lived access token (15–60 min) + refresh token rotation

### Multi-Factor Authentication (MFA)
- TOTP (Time-based One-Time Password) — Google Authenticator
- SMS OTP (less secure — SIM swap attack)
- Push notification / hardware key (most secure)

---

## 7. CORS (Cross-Origin Resource Sharing)

### Concept
Browser blocks cross-origin requests by default (same-origin policy). CORS headers tell browser which origins are allowed.

### Key Headers
- `Access-Control-Allow-Origin: https://app.company.com` — allowed origin(s)
- `Access-Control-Allow-Methods: GET, POST, PUT` — allowed methods
- `Access-Control-Allow-Headers: Authorization, Content-Type`
- `Access-Control-Allow-Credentials: true` — allow cookies (use với specific origin, không `*`)

### Common Mistakes
- `Access-Control-Allow-Origin: *` with credentials — browsers block this
- Reflecting any `Origin` header without validation — allows any origin
- Forgetting preflight (OPTIONS) response for non-simple requests

### Spring Boot CORS
- `@CrossOrigin` per controller, hoặc global `CorsConfigurer` trong `WebMvcConfigurer`

---

## 8. Sensitive Data Exposure

### Data at Rest
- Encrypt sensitive data: PII, payment info, health records
- Key management: AWS KMS, HashiCorp Vault — không hardcode keys
- DB encryption: Transparent Data Encryption (TDE)

### Data in Transit
- TLS 1.2+ — disable TLS 1.0/1.1
- HSTS header: `Strict-Transport-Security: max-age=31536000; includeSubDomains`
- Certificate pinning for mobile apps

### Data Minimization
- Không log PII/sensitive data
- Mask in responses: credit card `****-****-****-1234`
- GDPR/data retention: xóa data khi không còn cần

---

## 9. SSRF (Server-Side Request Forgery)

### Attack
Attacker cung cấp URL → server fetch URL đó → attacker trick server request internal services:
- `http://169.254.169.254/latest/meta-data/` (AWS EC2 metadata)
- `http://internal-db:5432/` (internal DB)
- `http://localhost:8080/admin` (local admin)

### Defense
- Whitelist allowed domains/IPs (không blacklist — bypass dễ)
- Block requests to private IP ranges (10.x, 172.16.x, 192.168.x, 127.x, 169.254.x)
- Use cloud provider's SSRF protection (IMDSv2 on AWS requires token)
- Validate URL scheme: chỉ cho phép `https://`, không `file://`, `gopher://`

---

## 10. Security Headers Checklist

| Header | Value | Protects Against |
|--------|-------|-----------------|
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains` | Downgrade attacks |
| `Content-Security-Policy` | `default-src 'self'` | XSS |
| `X-Content-Type-Options` | `nosniff` | MIME sniffing |
| `X-Frame-Options` | `DENY` | Clickjacking |
| `Referrer-Policy` | `no-referrer-when-downgrade` | Information leakage |
| `Permissions-Policy` | `camera=(), microphone=()` | Feature abuse |

---

## 11. Dependency Security

- **CVE monitoring:** Dependabot, Snyk, OWASP Dependency-Check
- **Keep dependencies updated:** especially security patches
- **Audit regularly:** `mvn dependency-check:check` hoặc GitHub security alerts
- **Lock file:** Maven/Gradle lock → reproducible builds, detect tampering
- **Private registry:** Nexus/Artifactory — control what packages are used

---

## 12. Interview Topics

### Câu hỏi hay gặp
- "Làm sao prevent SQL injection?" → Parameterized queries/ORM, không string concat
- "OWASP Top 10 là gì?" → Broken Access Control #1, Injection #3, etc. — biết ít nhất top 5
- "Làm sao store passwords?" → BCrypt/Argon2 with salt, không MD5/SHA1
- "CSRF attack là gì, defense thế nào?" → CSRF token + SameSite cookie
- "CORS là gì?" → Browser same-origin policy; headers cho phép cross-origin; không wildcard với credentials
- "SSRF là gì?" → Server bị trick fetch internal URLs; whitelist, block private IPs
- "JWT security concerns?" → Short TTL, secure storage (không localStorage), refresh token rotation
- "Làm sao secure API?" → AuthN+AuthZ, rate limiting, HTTPS, security headers, input validation, audit logs
