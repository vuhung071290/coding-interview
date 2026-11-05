# Testing Strategy for Java Backend Engineers

> Từ unit tests đến contract tests — chiến lược testing cho production-quality code

## Tại sao Testing quan trọng trong Interview
- Senior engineer phải nói được testing strategy, không chỉ "viết unit tests"
- Interview hay hỏi: "Làm sao test microservice của bạn?" hoặc "Walk me through testing pyramid"
- Testing skills phân biệt senior vs junior engineer

---

## 1. Test Pyramid Strategy

        /\
       /E2E\        ← ít nhất, chậm nhất, đắt nhất
      /------\
     /Integration\ ← vừa
    /------------\
   /  Unit Tests  \ ← nhiều nhất, nhanh nhất, rẻ nhất
  /--------------\

### Test Types
| Type | Scope | Speed | Use For |
|------|-------|-------|---------|
| Unit | 1 class/method, mock dependencies | ms | Business logic |
| Integration | Multiple components (service + DB) | seconds | Data flows |
| Contract | API compatibility between services | seconds | Microservice boundaries |
| E2E | Full system flow | minutes | Critical user journeys |

### Coverage Target
- Unit: aim 70–80% (100% has diminishing returns)
- Integration: cover happy path + critical error paths
- E2E: smoke test only critical journeys

---

## 2. JUnit 5 Essentials

### Key Annotations
- `@Test` — test method
- `@BeforeEach` / `@AfterEach` — setup/teardown per test
- `@BeforeAll` / `@AfterAll` — once per class (must be static)
- `@DisplayName("Should create order when items valid")` — readable test names
- `@Disabled("WIP")` — skip test
- `@Nested` — group related tests in inner class
- `@ParameterizedTest` — run same test with multiple inputs

### Parameterized Test Sources
- `@ValueSource(strings = {"", " ", "null"})` — simple single values
- `@CsvSource({"100, USD, true", "0, USD, false"})` — multiple parameters
- `@MethodSource("provideTestData")` — complex objects from factory method
- `@EnumSource(OrderStatus.class)` — all enum values

### Assertions — AssertJ (preferred over JUnit assertions)
- `assertThat(result.getStatus()).isEqualTo(OrderStatus.CREATED)`
- `assertThat(list).hasSize(3).extracting(Order::getId).containsExactly(1L, 2L, 3L)`
- `assertThatThrownBy(() -> service.getOrder(-1)).isInstanceOf(NotFoundException.class)`
- `assertThat(response).satisfies(r -> { ... })` — multiple conditions

---

## 3. Mockito

### Setup
- `@ExtendWith(MockitoExtension.class)` — JUnit 5 integration
- `@Mock` — create mock object
- `@InjectMocks` — inject mocks into subject under test
- `@Captor` — capture method arguments for assertion

### Stubbing
- `when(repo.findById(1L)).thenReturn(Optional.of(user))` — return value
- `when(service.call()).thenThrow(new ServiceException())` — throw exception
- `doReturn(value).when(spy).method()` — for spies (use to avoid actual call)

### Verification
- `verify(repo).save(any(Order.class))` — verify method called
- `verify(repo, times(2)).save(any())` — called exactly N times
- `verify(repo, never()).delete(any())` — verify NOT called
- `verifyNoMoreInteractions(repo)` — no unexpected calls

### Argument Captor
- Capture method arguments → assert on what was passed
- `emailCaptor.getValue().getTo()` — inspect captured argument

### Spy vs Mock
- **Mock:** all methods return default (null, 0, false)
- **Spy:** wraps real object, real methods unless stubbed — use for partial mocking

---

## 4. Spring Boot Test Slices

### @WebMvcTest — Controller layer only
- Loads only web layer (controllers, filters, security config)
- Fast — no DB, service, or repository beans
- Uses `MockMvc` for HTTP simulation
- Use `@MockBean` for service dependencies
- Tests: request mapping, response status, response body, validation, auth

### @DataJpaTest — Repository layer only
- Loads JPA context + embedded H2 by default
- `@AutoConfigureTestDatabase(replace = NONE)` to use real DB (Testcontainers)
- Tests: custom queries, native SQL, constraints, indexes
- Each test wrapped in transaction (auto-rollback)

### @SpringBootTest — Full integration
- Full application context
- `webEnvironment = RANDOM_PORT` for real HTTP server
- Use `TestRestTemplate` or `WebTestClient` for HTTP calls
- Slower — use sparingly for critical flows

### @MockBean vs @Mock
- `@Mock` — Mockito mock, no Spring context (use in pure unit tests)
- `@MockBean` — registers mock in Spring context, replaces real bean

---

## 5. Testcontainers

### Why Testcontainers
- In-memory H2 doesn't behave like PostgreSQL production (different SQL dialect, missing features)
- Mock repository tests don't test actual SQL, indexes, constraints
- Testcontainers: runs real Docker containers in tests

### Setup Pattern
- Annotate test class with `@Testcontainers`
- Declare `@Container static PostgreSQLContainer<?>` — static = shared across tests
- `@DynamicPropertySource` — override Spring properties with container's connection details
- `GenericContainer` for Redis, Kafka, Elasticsearch, etc.

### Container Reuse (Performance)
- `withReuse(true)` — container survives between test class runs
- Singleton pattern: static block initialization — reuse across JVM
- Significantly reduces test suite time for integration tests

### Available Containers
- `PostgreSQLContainer`, `MySQLContainer`, `MongoDBContainer`
- `KafkaContainer`, `LocalStackContainer` (AWS), `ElasticsearchContainer`
- `GenericContainer` — any Docker image

---

## 6. WireMock — Mock External HTTP Services

### Why WireMock
- Tests depending on external APIs (payment gateway, SMS, email) are flaky, slow, expensive
- WireMock intercepts HTTP calls, returns configured responses

### Capabilities
- Stub HTTP responses: status code, headers, body, delays
- Simulate error scenarios: timeouts, 500 errors, network failures
- Verify calls were made: URL, method, headers, body matching
- Record/playback mode: capture real API responses → replay in tests

### Patterns
- `@WireMockTest(httpPort = 8089)` — annotation-based setup
- `stubFor(post(urlEqualTo("/api/charge")).willReturn(aResponse().withStatus(200).withBody(...)))`
- `verify(postRequestedFor(urlEqualTo("/api/charge")).withRequestBody(matchingJsonPath("$.amount")))`
- Simulate timeout: `.willReturn(aResponse().withFixedDelay(5000))`

---

## 7. Contract Testing với Pact

### Problem with Traditional Integration Tests
- Consumer (mobile app) and Provider (API) test separately → no guarantee of compatibility
- E2E tests are slow and flaky
- API changes by provider can silently break consumers

### Consumer-Driven Contract Testing Flow
1. Consumer writes contract: "I need fields `orderId` and `status` in response"
2. Contract stored in Pact Broker (or file)
3. Provider verifies contract in CI pipeline
4. If Provider change breaks contract → CI fails → cannot deploy

### Key Concepts
- **Pact file:** JSON describing interactions (request + response expectations)
- **Pact Broker:** centralized contract storage and verification tracking
- **Consumer test:** generates pact file from expectations
- **Provider test:** verifies pact against real implementation
- **State:** setup precondition (`@State("order 123 exists")`) before each interaction

### Benefits
- Catch breaking API changes early (not in E2E or production)
- Consumer teams can evolve independently with confidence
- Living documentation of service interactions

---

## 8. Testing Best Practices

### Test Naming Patterns
- Pattern: `should[ExpectedBehavior]when[Condition]`
- Examples: `shouldThrowExceptionWhenUserNotFound`, `shouldReturnEmptyListWhenNoOrdersExist`
- Keep condition obvious from test code, not just name

### AAA Pattern (Arrange-Act-Assert)
- **Arrange:** setup data, configure mocks
- **Act:** single action — the thing being tested
- **Assert:** verify outcome — single responsibility
- One logical assertion per test (can have multiple `assertThat`)

### Test Isolation
- Tests must not depend on each other — no shared state
- `@Transactional` + rollback or `@DirtiesContext` to reset DB state
- Testcontainers: truncate tables in `@BeforeEach` for clean state

### Avoid Over-mocking
- Mock only external dependencies (DB, HTTP calls, messaging)
- Do NOT mock domain objects, value objects, utility classes
- Too many mocks → tests that don't verify actual behavior
- If you need to mock a class heavily, consider refactoring

### TDD Workflow
1. **Red** — write failing test (define expected behavior)
2. **Green** — implement minimum code to pass
3. **Refactor** — clean up without breaking tests

Not required for all code, but useful for complex business logic and bug fixes (write test reproducing bug first).

---

## 9. Interview Topics

### Câu hỏi hay gặp
- "Test pyramid là gì?" → Unit → Integration → E2E; most tests at bottom
- "Testcontainers vs H2?" → Real DB behavior vs fast in-memory; Testcontainers for critical DB behavior
- "Làm sao test code gọi external payment gateway?" → WireMock: stub HTTP, verify calls
- "Contract testing là gì?" → Pact, consumer-driven, verify compatibility without E2E
- "Làm sao achieve 80% coverage có nghĩa?" → Cover happy path + critical error paths; not random 80%
- "Flaky tests — nguyên nhân và cách fix?" → Timing issues (use Awaitility), shared state, external deps (mock them)
- "Mock vs Spy?" → Mock = all stubs; Spy = real object with selective stubbing
- "@WebMvcTest vs @SpringBootTest?" → Slice tests are faster; use SpringBootTest only for full flows
