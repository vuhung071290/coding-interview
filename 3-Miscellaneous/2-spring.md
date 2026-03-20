# SPRING & SPRING BOOT & HIBERNATE

---

# 1. Spring Core & IoC / DI

## 1.1 IoC & DI
- IoC container: quản lý lifecycle + dependencies.
- DI types: Constructor (best), Setter (optional deps), Field (tránh dùng).
- Autowiring: byType → byName; @Qualifier khi có nhiều bean cùng type.
- BeanFactory vs ApplicationContext (AC hỗ trợ AOP, event, i18n).
- @Component / @Service / @Repository / @Controller.
- Java config (@Configuration, @Bean) > XML.

### Senior+
- Circular dependencies & cách Spring giải quyết (constructor injection tránh được).
- Khi nào dùng @Lazy.
- Component scan pitfalls: packages lồng nhau, multiple context.

---

# 2. Bean Lifecycle & Scope

- Scopes: singleton, prototype, request/session (web), application.
- Lifecycle: instantiation → populate → Aware → BeanPostProcessor → init (@PostConstruct) → sử dụng → destroy.
- @DependsOn, lazy-init, custom BeanPostProcessor.

### Senior+
- Difference giữa BeanFactoryPostProcessor vs BeanPostProcessor.
- Proxy mode trong scope (request-scoped bean inside singleton bean).
- Thread-safety của singleton bean.

---

# 3. Spring AOP

- Cross-cutting concerns (logging, metrics, transaction).
- Concepts: Aspect, JoinPoint, Advice, Pointcut, Weaving.
- Spring AOP dùng proxy (JDK dynamic proxy hoặc CGLIB).
- Advice types: @Before / @After / @AfterReturning / @AfterThrowing / @Around.

### Senior+
- Limitations: chỉ method-level, không hỗ trợ field-level weaving như AspectJ.
- Self-invocation issue (method A gọi method B cùng class → AOP không chạy).
- Performance overhead & best practices.

---

# 4. Spring MVC & REST

- DispatcherServlet flow: request → HandlerMapping → HandlerAdapter → Controller → ViewResolver/MessageConverter.
- @Controller vs @RestController.
- Binding: @RequestParam, @PathVariable, @RequestBody, @ResponseStatus.
- Validation: @Valid, BindingResult.
- Exception handling: @ControllerAdvice + @ExceptionHandler.
- Filter vs HandlerInterceptor.

### Senior+
- Content negotiation (JSON/XML).
- Best practice API versioning.
- Idempotency cho POST/PUT/PATCH.
- CORS config chuẩn cho microservices.

---

# 5. REST API Advanced

- HTTP caching: ETag, Last-Modified.
- Pageable, Sorting.
- OpenAPI/Swagger.
- HATEOAS (optional).

### Senior+
- Correlation ID (trace logging).
- Anti-patterns: over-fetching / under-fetching.
- Bulk API design.

---

# 6. Spring Boot Core

- @SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan.
- Starter dependencies.
- auto-configuration: @ConditionalOnClass, @ConditionalOnMissingBean.
- application.yml hierarchy, profile override.

### Senior+
- Debug auto-configuration: `--debug`, `ConditionEvaluationReport`.
- Khi nào disable auto-config (exclude).
- Custom starter (ý tưởng).

---

# 7. Spring Boot Actuator & Observability

- Actuator endpoints: /health, /metrics, /env, /beans, /info.
- Micrometer: metrics, counter, gauge.

### Senior+
- Custom HealthIndicator (DB, Redis, downstream).
- Distributed tracing: OpenTelemetry (new), Zipkin/Jaeger.
- Logging best-practices: MDC for traceId.

---

# 8. Spring Boot Testing

- Test slices: @WebMvcTest, @DataJpaTest.
- @SpringBootTest cho integration.
- MockMvc, TestRestTemplate, WebTestClient.
- @MockBean, @SpyBean.

### Senior+
- Test context caching (giảm thời gian build).
- Database transactional test & pitfalls.
- Test embedded server ports (random port).

---

# 9. JPA / Hibernate – Mapping

- Entities: @Id, @GeneratedValue (IDENTITY, SEQUENCE, AUTO,…)
- Relations: OneToOne, OneToMany, ManyToOne, ManyToMany.
- Cascade types, orphanRemoval.
- Inheritance: SINGLE_TABLE (fast), JOINED (normalized), TABLE_PER_CLASS.

### Senior+
- N+1 detection & fix (fetch join, EntityGraph).
- LazyInitializationException & cách tránh.
- Pros/cons bi-directional mapping.

---

# 10. Persistence Context & Querying

- States: transient → managed → detached → removed.
- persist vs merge.
- flush, clear, dirty checking.
- Repositories: JpaRepository, @Query, JPQL, native, Specifications.
- Pagination & Sorting.

### Senior+
- Batch operations (hibernate.jdbc.batch_size).
- 1st-level cache vs 2nd-level cache (Ehcache, Hazelcast).
- Pessimistic vs Optimistic locking.

---

# 11. Transaction Management

- @Transactional propagation: REQUIRED, REQUIRES_NEW, MANDATORY, SUPPORTS…
- Isolation levels: READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE.
- Rollback rules: unchecked auto rollback, checked needs cấu hình.
- Transactional boundary đặt ở service layer.

### Senior+
- Pitfalls: self-invocation làm mất @Transactional.
- READ_COMMITTED + Lazy loading → inconsistent reads.
- Nested transactions & savepoints.
- Saga / Outbox pattern khi giao tiếp giữa microservices.

---

# 12. Spring Security (Compact but Senior)

- Authentication vs Authorization.
- SecurityFilterChain (Spring Security 6).
- UserDetailsService, PasswordEncoder.
- Method security: @PreAuthorize.
- JWT: filter chain, authentication manager.

### Senior+
- Stateless authentication đúng chuẩn.
- OAuth2/OIDC fundamentals.
- Custom filter → addFilterBefore/After.
- Common vulnerabilities: CSRF, session fixation.

---

# 13. Microservices & Cloud (Essential Only)

- Config Server, Eureka, Feign, Gateway.
- Circuit breaker: Resilience4j.
- Distributed tracing.

### Senior+
- API composition vs Orchestration.
- Retry patterns (exponential backoff).
- Bulkhead, rate limit, timeouts.

---

# 14. Performance & Deployment

- Caching: Spring Cache + Redis.
- Connection pool tuning (HikariCP).
- Dockerfile chuẩn.
- CI/CD basics.

### Senior+
- JVM tuning (heap, GC).
- Blue-Green deploy, rolling update.
- Observability SRE (RED, USE metrics).

---

# 15. Interview Checklist (Quick)

- DI types & lifecycle.
- AOP: proxy-based.
- DispatcherServlet flow.
- @SpringBootApplication & Auto-config.
- N+1 & Lazy loading.
- Transaction propagation.
- JWT authentication flow.
- Circuit breaker.
- Caching & Connection pool tuning.
