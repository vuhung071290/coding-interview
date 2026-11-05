# Spring & Spring Boot for Backend Engineers

> Comprehensive guide to Spring ecosystem for enterprise backend development

## 1. Spring Core & Dependency Injection

### 1.1 Inversion of Control (IoC) & Dependency Injection
- Spring container manages object lifecycle and dependencies
- **Constructor injection:** Preferred — guarantees required dependencies, immutable
- **Setter injection:** Optional dependencies
- **Field injection:** Avoid — hides dependencies, testing difficult

**Bean registration:**
- `@Component`, `@Service`, `@Repository`, `@Controller` — stereotype annotations
- `@Configuration + @Bean` — explicit bean definition (preferred over XML)
- `@Autowired` — inject by type; `@Qualifier` to disambiguate

### 1.2 Bean Scopes & Lifecycle
| Scope | Instances | Use When |
|-------|----------|----------|
| Singleton | 1 per container | Default; must be stateless or thread-safe |
| Prototype | New per request | Stateful beans |
| Request | 1 per HTTP request | Web: request-scoped data |
| Session | 1 per HTTP session | Web: session state |

**Bean lifecycle:** Instantiate → Populate properties → BeanPostProcessor → `@PostConstruct` → Ready → `@PreDestroy`

### 1.3 Common IoC Issues
- **Circular dependencies:** Constructor injection prevents them; use `@Lazy` as workaround
- **Self-invocation:** AOP proxy not triggered for calls within same class
- **Thread safety:** Singleton beans must be stateless — no instance variables for request data

---

## 2. Spring AOP & Cross-cutting Concerns

### AOP Fundamentals
- **Aspect:** modularization of a cross-cutting concern
- **Join Point:** point in execution where advice can be applied
- **Pointcut:** expression matching join points
- **Advice types:** `@Before`, `@After`, `@Around`, `@AfterReturning`, `@AfterThrowing`

**Spring AOP characteristics:**
- Proxy-based: JDK dynamic proxy (interface) or CGLIB (class)
- Method-level only — no field access or constructor interception
- Runtime weaving

**Common use cases:** `@Transactional`, `@Cacheable`, `@PreAuthorize`, logging, auditing

**Key limitation:** Self-invocation in same class doesn't trigger AOP — use separate bean or `ApplicationContext.getBean()`

---

## 3. Spring MVC & RESTful APIs

### DispatcherServlet Flow
Request → HandlerMapping → HandlerAdapter → Controller → ModelAndView → ViewResolver

### Key Annotations
- `@RestController` = `@Controller` + `@ResponseBody`
- `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`
- `@RequestParam`, `@PathVariable`, `@RequestBody`
- `@Valid` — trigger JSR-303 bean validation
- `@ControllerAdvice` — global exception handler
- `@ExceptionHandler` — method-specific exception handling

### REST Best Practices
- Consistent error response format (RFC 9457 in Spring Boot 3)
- Proper HTTP status codes
- ETag / `Last-Modified` for HTTP caching
- Pagination with `Pageable` parameter
- CORS configuration for cross-origin clients
- Correlation IDs for distributed tracing

---

## 4. Spring Boot & Auto-configuration

### `@SpringBootApplication` = three annotations
- `@Configuration` — config source
- `@EnableAutoConfiguration` — Spring Boot auto-config
- `@ComponentScan` — scan current package

### Auto-configuration Mechanism
- Conditional annotations: `@ConditionalOnClass`, `@ConditionalOnMissingBean`, `@ConditionalOnProperty`
- `spring.factories` / `AutoConfiguration.imports` lists candidate configurations
- Only activated when classpath/properties conditions met

### Configuration Priority (highest → lowest)
1. Command line arguments
2. Environment variables
3. `application.yml` / `application.properties`
4. Default values in code

### Profiles
- `application-{profile}.yml` overrides default
- Activate: `spring.profiles.active=prod`
- `@Profile("prod")` on beans for profile-specific wiring

### Actuator Endpoints
- `/actuator/health` — application health (DB, disk, custom checks)
- `/actuator/metrics` — JVM + custom metrics
- `/actuator/info` — app metadata
- `/actuator/env` — environment properties (caution: sensitive data)

---

## 5. Data Access & JPA/Hibernate

### Entity Basics
- `@Entity`, `@Id`, `@GeneratedValue` (IDENTITY, SEQUENCE, AUTO)
- Relationship: `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`
- Cascade types: PERSIST, MERGE, REMOVE, ALL

### Entity States
- **Transient:** not associated with persistence context
- **Managed:** tracked, changes auto-saved on commit
- **Detached:** was managed, session closed
- **Removed:** scheduled for deletion

### Performance Optimization
- **N+1 problem:** use `@EntityGraph` or JPQL `JOIN FETCH`
- **Lazy loading:** default for collections — configure explicitly
- **Batch operations:** `hibernate.jdbc.batch_size`
- **Caching:** 1st level (session), 2nd level (Ehcache, Redis)

### Transaction Management
- `@Transactional` — service layer, not repository
- Propagation: REQUIRED (default), REQUIRES_NEW, MANDATORY
- Isolation: READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
- Unchecked exceptions auto-rollback; checked need explicit `rollbackFor`

**Pitfalls:** Self-invocation bypasses transaction proxy; `LazyInitializationException` outside transaction; long transactions exhaust connection pool

---

## 6. Spring Security

### Core Concepts
- **Authentication:** who you are
- **Authorization:** what you can access
- **SecurityFilterChain:** ordered filters processing every request

### Authentication Mechanisms
- `UserDetailsService` — load user from DB
- `PasswordEncoder` — BCrypt recommended
- JWT — stateless token-based auth
- OAuth2/OIDC — third-party auth (Keycloak, Auth0, Okta)

### Method Security
- `@PreAuthorize("hasRole('ADMIN')")` — before method
- `@PostAuthorize` — after method
- `@Secured` — simple role check

### Production Configuration
- Stateless design: no server-side sessions, JWT in `Authorization` header
- CSRF: disable for stateless REST APIs
- CORS: explicit configuration for SPA clients
- Security headers: HSTS, CSP, X-Frame-Options

---

## 7. Spring Boot 3.x / Spring 6 — Modern Spring

> Java 17+ required. Breaking changes from Spring Boot 2.x.

### Jakarta EE Migration (Breaking)
- **All** `javax.*` → `jakarta.*` (exception: `javax.xml.*`)
- Affects: `javax.persistence` → `jakarta.persistence`, `javax.validation` → `jakarta.validation`, `javax.servlet` → `jakarta.servlet`
- Hibernate 6, Tomcat 10, Jetty 11 also migrated

### Problem Details RFC 9457 (Built-in)
- Enable: `spring.mvc.problemdetails.enabled=true`
- Spring automatically formats error responses as RFC 9457 JSON
- Fields: `type`, `title`, `status`, `detail`, `instance`

### Micrometer Tracing + OpenTelemetry
- Replaces Spring Cloud Sleuth (deprecated)
- Dependencies: `micrometer-tracing-bridge-otel` + `opentelemetry-exporter-otlp`
- Auto-traces: HTTP requests, JDBC queries, Kafka messages
- TraceId/SpanId auto-propagated to MDC → appear in logs

### Virtual Threads (Spring Boot 3.2+)
- Enable: `spring.threads.virtual.enabled=true`
- Tomcat uses virtual threads per request automatically
- No code changes needed — blocking code scales like reactive

### GraalVM Native Image
- Build: `./mvnw -Pnative native:compile`
- Result: ~50MB executable, startup < 100ms (vs JVM 2–5s)
- Use when: Lambda functions, CLI tools, fast startup required
- Trade-offs: slow build (5–10 min), no dynamic class loading

### Spring Authorization Server
- Official OAuth2 authorization server (replaces community solutions)
- Supports: Authorization Code, Client Credentials, Refresh Token, PKCE
- Configure: `RegisteredClientRepository`, `JWKSource`, token customization

### Spring WebFlux — When to Use
- Reactive programming with Project Reactor (Mono/Flux)
- R2DBC for non-blocking database access
- **Use WebFlux when:** streaming, SSE, full reactive pipeline required
- **Use Virtual Threads when:** existing blocking code, simpler codebase, most I/O-bound apps

---

## 8. Microservices & Production

### Spring Cloud Components
- **Eureka:** service registry and discovery
- **Config Server:** centralized configuration
- **Gateway:** API gateway with routing, filters

### Resilience Patterns
- **Circuit Breaker (Resilience4j):** prevent cascading failures
- **Retry with backoff:** transient failure handling
- **Bulkhead:** isolate critical resources

### Connection Pooling
- HikariCP (default in Spring Boot) — tune `maximum-pool-size`, `connection-timeout`
- Match pool size to expected concurrency

---

## 9. Testing Spring Applications

### Test Slices (faster than full context)
- `@WebMvcTest` — web layer only (controllers, filters, security)
- `@DataJpaTest` — JPA repositories + embedded DB
- `@SpringBootTest` — full application context

### Test Tools
- `@MockBean` — replace Spring bean with Mockito mock
- `MockMvc` — test controllers without real HTTP
- `TestRestTemplate` — integration testing with real HTTP
- `@ActiveProfiles("test")` — activate test profile

---

## 📚 Interview Quick Reference

**Must-know concepts:**
- DI types and when to use each
- Bean scopes and lifecycle
- AOP proxy mechanism and self-invocation limitation
- Spring MVC DispatcherServlet flow
- `@Transactional` propagation and isolation levels
- Auto-configuration conditional mechanism

**Common interview questions:**
- "How does Spring's dependency injection work?" → IoC container, proxy-based DI
- "What happens when you annotate with @Transactional?" → AOP proxy, propagation, rollback rules
- "How do you solve the N+1 query problem?" → @EntityGraph, JOIN FETCH
- "Breaking changes Spring Boot 2 → 3?" → javax→jakarta, Java 17 minimum
- "Virtual Threads vs WebFlux in Spring?" → Virtual for most apps; WebFlux for streaming
- "GraalVM Native when?" → Lambda, CLI, fast startup needs
