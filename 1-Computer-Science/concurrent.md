# Concurrency & Parallelism for Backend Engineers

> Understanding concurrent programming for scalable backend systems

# 1. Fundamental Concepts

## Concurrency vs Parallelism
- **Concurrency:** Organizing multiple tasks to make progress together — about structure
- **Parallelism:** Actually running multiple tasks simultaneously — about execution
- Single-core: có thể concurrency (time-slicing) nhưng không parallelism
- Multi-core: có cả hai

## Threading Models — When to Use

| Model | Mechanism | Best for | Examples |
|-------|----------|---------|---------|
| Multithreading | OS threads, shared memory | CPU-bound | Java threads, .NET |
| Async/Event Loop | Single thread, non-blocking I/O | I/O-bound | Node.js, Python asyncio |
| Actor Model | Isolated processes + message passing | Distributed, fault-tolerant | Erlang, Akka |
| Virtual Threads | JVM-managed, cheap threads | I/O-bound at scale (Java 21) | Project Loom |

**Rule of thumb:** I/O-bound → Async/Virtual Threads; CPU-bound → Multithreading

---

# 2. Synchronization Primitives

## Common Problems
- **Race Condition:** multiple threads accessing shared state, outcome depends on timing
- **Deadlock:** threads waiting for each other indefinitely
- **Starvation:** thread cannot get resources, indefinitely postponed

## Primitives
- **Mutex/Lock:** mutual exclusion — 1 thread at a time in critical section
- **Semaphore:** counting lock — N threads allowed concurrently
- **Read-Write Lock:** multiple readers OR single writer
- **Atomic Operations:** hardware-level guarantee for simple ops (CAS, compare-and-swap)
- **Condition Variables:** wait/notify mechanism for thread coordination

## Thread-Safe Strategies
- **Lock-based:** synchronized, ReentrantLock — simple, potential contention
- **Lock-free:** Atomic classes, CAS — faster, harder to reason about
- **Immutable data:** no mutation → no synchronization needed
- **Thread-local storage:** each thread has its own copy
- **Concurrent collections:** ConcurrentHashMap, CopyOnWriteArrayList

---

# 3. Java Concurrency Tools

## Executors Framework
- **FixedThreadPool:** fixed N threads — predictable resource usage
- **CachedThreadPool:** create threads as needed, reuse idle — unbounded, risk of OOM
- **ScheduledExecutorService:** delayed and periodic execution
- **VirtualThreadPerTaskExecutor:** Java 21 — 1 virtual thread per task

## CompletableFuture (Async pipelines)
- Chain async operations: `thenApply`, `thenCompose`, `thenCombine`
- Error handling: `exceptionally`, `handle`
- Combine multiple futures: `allOf`, `anyOf`

## Memory Model (JMM)
- **volatile:** ensures visibility across threads, prevents reordering — not atomic
- **synchronized:** mutual exclusion + memory synchronization
- **happens-before:** A happens-before B → A's effects visible to B
  - Thread start/join, lock acquire/release, volatile write/read, final field init

---

# 4. Virtual Threads (Java 21) — Project Loom

> Giải quyết "thread per request" bottleneck mà không cần reactive programming

## Vấn đề với Platform Threads
- 1 request → 1 OS thread (platform thread) ≈ 1MB stack
- 10K threads × 1MB = 10GB RAM chỉ cho thread stacks
- Blocking I/O (DB query, HTTP call): thread bị "park" — lãng phí OS thread
- Throughput bị giới hạn bởi số threads, không phải CPU hay I/O capacity

## Virtual Thread Model
- 1 request → 1 virtual thread (JVM-managed, không phải OS thread)
- 1 JVM **carrier thread** (OS thread) multiplexes NHIỀU virtual threads
- Khi virtual thread bị block → unmount từ carrier thread → carrier chạy thread khác
- Virtual thread được mount lại khi ready to continue
- Chi phí: vài KB/virtual thread → có thể chạy hàng triệu

## So sánh

| | Platform Thread | Virtual Thread | Reactive (WebFlux) |
|---|---|---|---|
| Memory per unit | ~1MB | ~few KB | ~minimal |
| Programming model | Simple (blocking) | **Simple (blocking)** | Complex (non-blocking) |
| I/O scaling | Limited | **Excellent** | Excellent |
| CPU-bound | Good | Same as platform | Same |
| Ecosystem compatibility | Full | Full | Limited |

## Pinning — Vấn đề quan trọng
- Virtual thread bị **pinned** (không thể unmount) khi trong `synchronized` block
- Pinned → carrier thread bị block → defeats the purpose
- **Fix:** dùng `ReentrantLock` thay `synchronized` trong I/O paths
- Native method calls (JNI) cũng gây pinning — không thể fix

## Structured Concurrency
- Chạy parallel tasks theo cây phân cấp — parent task quản lý child tasks
- Nếu 1 child fail → tất cả siblings tự động bị cancel
- Tránh **task leaks** (tasks tiếp tục chạy sau khi scope đóng)
- API: `StructuredTaskScope.ShutdownOnFailure`, `ShutdownOnSuccess`

## ScopedValue (Java 21) — thay ThreadLocal
- ThreadLocal: shared mutable per-thread state → memory leaks trong thread pools
- ScopedValue: immutable, scoped to a task tree → better fit với virtual threads

## Spring Boot 3.2+
- Bật `spring.threads.virtual.enabled=true` → Tomcat dùng virtual threads tự động
- Không cần đổi code — existing blocking code scales tốt

## Virtual Threads vs Reactive
- **Virtual Threads:** dùng blocking libraries (JDBC, RestTemplate), simpler code, great for most I/O-bound apps
- **Reactive/WebFlux:** cần streaming, back-pressure, SSE, full reactive pipeline — more complex
- **2024+ recommendation:** Virtual Threads trừ khi có specific reactive requirements

## Interview Questions
- "Virtual Thread là gì?" → Lightweight JVM-managed threads, millions possible, blocking I/O không block OS thread
- "Platform Thread vs Virtual Thread?" → OS-managed 1MB vs JVM-managed few KB
- "Pinning là gì, làm sao tránh?" → synchronized → ReentrantLock trong I/O-heavy paths
- "Virtual Threads có thay thế hoàn toàn Reactive không?" → Cho I/O-bound apps thì có; streaming/SSE vẫn cần Reactive

---

# 5. Reactive Programming

## Concepts
- Treat data as continuous **streams** — push-based, event-driven
- **Operators:** map, filter, flatMap, merge, zip, buffer
- **Backpressure:** slow consumers signal fast producers to slow down

## Tools
- **Project Reactor:** Flux (0-N items), Mono (0-1 item) — Spring WebFlux
- **RxJava:** Observable, Single, Completable
- **Kotlin Coroutines:** coroutine-based async (simpler than reactive)

## Use Cases
- Server-Sent Events (SSE), WebSocket streams
- Real-time data pipelines
- Non-blocking DB access (R2DBC)

---

# 6. Performance Optimization

## Common Bottlenecks
- **Context switching:** OS switching between threads (expensive for many threads)
- **Lock contention:** multiple threads waiting on same lock
- **False sharing:** different variables in same CPU cache line
- **Thread pool starvation:** all threads blocked on I/O

## Optimization Strategies
- Thread pools: reuse threads, right-size pool (N CPU + buffer for I/O blocking)
- Lock-free algorithms where possible
- Work-stealing queues (ForkJoinPool)
- Virtual threads for I/O-bound workloads

---

# 📚 Quick Reference

**Code interview patterns:** producer-consumer (BlockingQueue), thread pool implementations, rate limiters, circuit breakers

**Key discussion points:**
- Trade-offs between concurrency models (thread-per-request vs async vs virtual threads)
- How to debug race conditions and deadlocks
- Performance characteristics and bottlenecks
- Failure handling and graceful degradation

**Connection to other topics:**
- [Networking](networking.md): Async I/O, connection pooling
- [Databases](databases.md): Transaction isolation, connection management
- [Java](../3-Backend-Technology-Stack/java.md): Executor framework, concurrent collections
