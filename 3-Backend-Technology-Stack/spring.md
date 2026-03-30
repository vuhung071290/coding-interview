# Spring & Spring Boot for Backend Engineers

> Comprehensive guide to Spring ecosystem for enterprise backend development

## 🎯 Why Spring Matters for Backend Engineers
- **Industry standard:** Most Java enterprise applications use Spring
- **Microservices architecture:** Spring Boot simplifies microservice development
- **Production features:** Built-in monitoring, security, testing, deployment support
- **Interview frequency:** Spring questions appear in 90% of Java backend interviews
- **Ecosystem integration:** Seamless integration with databases, messaging, cloud services
- **Rapid development:** Convention over configuration, auto-configuration, starters

**Focus:** Production-ready Spring knowledge for scalable backend systems and technical interviews.

---

# 1. Spring Core & Dependency Injection

## 1.1 Inversion of Control (IoC) & Dependency Injection
**Core concept:** Spring container manages object lifecycle and dependencies

**Dependency Injection types:**
- **Constructor injection:** Preferred approach, guarantees required dependencies
- **Setter injection:** For optional dependencies
- **Field injection:** Avoid (makes testing difficult, hides dependencies)

**Bean registration:**
- **@Component, @Service, @Repository, @Controller:** Stereotype annotations
- **@Configuration + @Bean:** Explicit bean definition (Java config > XML)
- **@Autowired:** Inject dependencies by type, use @Qualifier for disambiguation

## 1.2 Bean Scopes & Lifecycle
**Bean scopes:**
- **Singleton:** Default, one instance per container (not thread-safe by default)
- **Prototype:** New instance per request
- **Request/Session:** Web-specific scopes
- **Application:** One per ServletContext

**Bean lifecycle:**
1. Instantiation → 2. Populate properties → 3. BeanPostProcessor → 4. @PostConstruct → 5. Ready to use → 6. @PreDestroy

## 1.3 Advanced IoC Concepts
**Common issues:**
- **Circular dependencies:** Constructor injection prevents them, use @Lazy as workaround
- **Proxy issues:** Self-invocation doesn't trigger AOP
- **Thread safety:** Singleton beans must be stateless or thread-safe

**Senior insight:** Understand when Spring creates proxies and how it affects method calls within the same class

---

# 2. Spring AOP & Cross-cutting Concerns

## 2.1 Aspect-Oriented Programming Fundamentals
**Core concepts:**
- **Aspect:** Modularization of cross-cutting concern (e.g., @Transactional)
- **Join Point:** Points in execution where aspects can be applied
- **Pointcut:** Expression that matches join points
- **Advice:** Action taken at join point (@Before, @After, @Around, etc.)

**Spring AOP characteristics:**
- **Proxy-based:** Uses JDK dynamic proxy or CGLIB
- **Method-level only:** Cannot intercept field access or constructor calls
- **Runtime weaving:** Aspects applied at runtime, not compile time

## 2.2 Common Use Cases & Limitations
**Typical applications:**
- **@Transactional:** Database transaction management
- **@Cacheable:** Method result caching
- **Security:** @PreAuthorize, @PostAuthorize
- **Logging/Auditing:** Cross-cutting logging concerns

**Important limitation:** Self-invocation doesn't trigger AOP
**Senior insight:** Use ApplicationContext.getBean() or separate service for self-invocation scenarios

---

# 3. Spring MVC & RESTful APIs

## 3.1 Spring MVC Architecture
**DispatcherServlet flow:**
1. Request → 2. HandlerMapping → 3. HandlerAdapter → 4. Controller → 5. ModelAndView → 6. ViewResolver

**Key annotations:**
- **@RestController:** @Controller + @ResponseBody for REST APIs
- **@RequestMapping, @GetMapping, @PostMapping:** Route mapping
- **@RequestParam, @PathVariable, @RequestBody:** Parameter binding
- **@Valid:** JSR-303 validation

## 3.2 REST API Best Practices
**Exception handling:**
- **@ControllerAdvice:** Global exception handling
- **@ExceptionHandler:** Method-specific exception handling
- Return consistent error response format

**Content negotiation:**
- Support multiple formats (JSON, XML) based on Accept header
- Use proper HTTP status codes
- Implement proper HTTP caching (ETag, Last-Modified)

**API versioning strategies:**
- URL path: `/api/v1/users`
- Header-based: `Accept: application/vnd.api.v1+json`
- Parameter-based: `/api/users?version=1`

## 3.3 Advanced REST Features
**Pagination & Sorting:**
- Use `Pageable` parameter for automatic pagination
- Support sorting with `Sort` parameter
- Return metadata (total count, page info)

**Security & CORS:**
- Configure CORS for microservices communication
- Implement proper authentication/authorization
- Use correlation IDs for distributed tracing

---

# 4. Spring Boot & Auto-configuration

## 4.1 Spring Boot Fundamentals
**@SpringBootApplication breakdown:**
- **@Configuration:** Marks class as configuration source
- **@EnableAutoConfiguration:** Enables Spring Boot's auto-configuration
- **@ComponentScan:** Scans for components in current package and below

**Starter dependencies:** Pre-configured dependency bundles
- `spring-boot-starter-web`: Web applications
- `spring-boot-starter-data-jpa`: JPA and Hibernate
- `spring-boot-starter-security`: Security features
- `spring-boot-starter-test`: Testing framework

## 4.2 Configuration & Profiles
**Configuration hierarchy (highest to lowest priority):**
1. Command line arguments
2. Environment variables
3. application.yml/properties files
4. Default values

**Profile-based configuration:**
- `application-{profile}.yml` overrides default
- Activate with `spring.profiles.active`
- Use @Profile annotation for profile-specific beans

## 4.3 Production Features & Monitoring
**Spring Boot Actuator:**
- `/actuator/health`: Application health status
- `/actuator/metrics`: Application metrics (JVM, custom)
- `/actuator/info`: Application information
- `/actuator/env`: Environment properties

**Custom monitoring:**
- Create custom HealthIndicator for database/external service checks
- Use Micrometer for custom metrics (counters, gauges, timers)
- Implement distributed tracing with correlation IDs

**Senior insight:** Auto-configuration works through conditional annotations (@ConditionalOnClass, @ConditionalOnMissingBean)

---

# 5. Data Access & JPA/Hibernate

## 5.1 JPA Entity Mapping & Relationships
**Entity basics:**
- **@Entity, @Id:** Basic entity definition
- **@GeneratedValue:** ID generation strategies (IDENTITY, SEQUENCE, AUTO)
- **@Column:** Column mapping and constraints

**Relationship mapping:**
- **@OneToOne:** Bidirectional or unidirectional
- **@OneToMany/@ManyToOne:** Most common relationship
- **@ManyToMany:** Requires join table
- **Cascade types:** PERSIST, MERGE, REMOVE, REFRESH, ALL

## 5.2 Persistence Context & Entity States
**Entity lifecycle:**
- **Transient:** Not associated with persistence context
- **Managed:** Tracked by persistence context, changes auto-saved
- **Detached:** Was managed but session closed
- **Removed:** Scheduled for deletion

**Key operations:**
- **persist():** Make transient entity managed
- **merge():** Merge detached entity back to managed
- **find() vs getReference():** Eager vs lazy loading

## 5.3 Querying & Performance
**Repository patterns:**
- **JpaRepository:** Full CRUD + pagination/sorting
- **@Query:** Custom JPQL or native SQL
- **Specifications:** Dynamic query building
- **@NamedQuery:** Reusable queries

**Performance optimization:**
- **N+1 problem:** Use @EntityGraph or fetch joins
- **Lazy loading:** Configure appropriate fetch strategies
- **Batch operations:** Set hibernate.jdbc.batch_size
- **Caching:** First-level (session) vs second-level (Ehcache)

## 5.4 Transaction Management
**@Transactional attributes:**
- **Propagation:** REQUIRED (default), REQUIRES_NEW, MANDATORY
- **Isolation:** READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
- **Rollback rules:** Unchecked exceptions auto-rollback, checked need configuration

**Common pitfalls:**
- **Self-invocation:** @Transactional doesn't work within same class
- **LazyInitializationException:** Accessing lazy collections outside transaction
- **Connection pool exhaustion:** Long-running transactions

**Senior insight:** Always place @Transactional at service layer, not repository layer

---

# 6. Security & Testing

## 6.1 Spring Security Fundamentals
**Core concepts:**
- **Authentication:** Who you are (login process)
- **Authorization:** What you can access (permissions)
- **SecurityFilterChain:** Series of filters processing requests

**Authentication mechanisms:**
- **UserDetailsService:** Load user details from database
- **PasswordEncoder:** Secure password hashing (BCrypt recommended)
- **JWT:** Stateless token-based authentication
- **OAuth2/OIDC:** Third-party authentication integration

## 6.2 Method-Level Security & Advanced Features
**Method security:**
- **@PreAuthorize:** Check permissions before method execution
- **@PostAuthorize:** Check after method execution
- **@Secured:** Simple role-based security

**Production considerations:**
- **Stateless design:** No server-side sessions, JWT in headers
- **CORS configuration:** Proper cross-origin setup for SPAs
- **CSRF protection:** Disable for stateless APIs, enable for traditional web apps

## 6.3 Testing Spring Applications
**Test slices (faster than full context):**
- **@WebMvcTest:** Test web layer only
- **@DataJpaTest:** Test JPA repositories only
- **@SpringBootTest:** Full integration test

**Mocking and test utilities:**
- **@MockBean:** Replace Spring beans with mocks
- **MockMvc:** Test web layer without HTTP server
- **TestRestTemplate:** Integration testing with real HTTP

**Best practices:**
- Use test slices for faster feedback
- Mock external dependencies
- Test with embedded databases (H2) when possible

---

# 7. Microservices & Production

## 7.1 Spring Cloud Ecosystem
**Service discovery:**
- **Eureka:** Service registry and discovery
- **Config Server:** Centralized configuration management
- **Gateway:** API gateway with routing and filtering

**Resilience patterns:**
- **Circuit breaker:** Prevent cascading failures (Resilience4j)
- **Retry patterns:** Exponential backoff for transient failures
- **Bulkhead:** Isolate critical resources

## 7.2 Production Deployment & Performance
**Containerization:**
- **Docker:** Layered builds, multi-stage builds
- **Spring Boot features:** Fat JAR, executable JAR, Docker image building

**Performance optimization:**
- **Connection pooling:** HikariCP configuration
- **Caching:** Spring Cache with Redis
- **JVM tuning:** Heap sizing, GC selection
- **Monitoring:** Actuator + Micrometer + monitoring systems

## 7.3 Distributed System Patterns
**Data consistency:**
- **Saga pattern:** Manage distributed transactions
- **Outbox pattern:** Reliable event publishing
- **Event sourcing:** Audit trail and state reconstruction

**Communication patterns:**
- **API composition:** Aggregate data from multiple services
- **CQRS:** Separate read and write models
- **Event-driven architecture:** Asynchronous service communication

**Senior insight:** Microservices add complexity - ensure team readiness and proper monitoring before adoption

---

# 📚 Essential Resources & Interview Preparation

## Interview Quick Reference
**Core Spring concepts (must know):**
- Dependency injection types and when to use each
- Bean scopes and lifecycle management
- AOP proxy mechanisms and limitations
- Spring MVC request flow (DispatcherServlet)
- Transaction propagation and isolation levels

**Spring Boot specifics:**
- Auto-configuration mechanism
- Actuator endpoints and monitoring
- Profile-based configuration
- Testing strategies and test slices

**Common interview questions:**
- "Explain how Spring's dependency injection works"
- "What happens when you annotate a method with @Transactional?"
- "How do you handle the N+1 query problem in JPA?"
- "Design a RESTful API with proper error handling"
- "How would you secure a Spring Boot application?"

## System Design Applications
**Spring in backend architecture:**
- Microservices with Spring Cloud
- Event-driven systems with Spring Integration
- Caching strategies with Spring Cache
- Security patterns for distributed systems
- Database scaling with JPA/Hibernate

## Production Considerations
**Performance optimization:**
- JVM tuning for Spring Boot applications
- Connection pool configuration (HikariCP)
- Caching strategies (Redis integration)
- Database query optimization (JPA/Hibernate)

**Deployment strategies:**
- Containerization with Docker
- Blue-green deployments
- Configuration management across environments
- Monitoring and observability setup

