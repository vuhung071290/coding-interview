# Concurrency & Parallelism for Backend Engineers

> Understanding concurrent programming for scalable backend systems

## 🎯 Why Concurrency Matters for Backend Engineers
- **Scalable applications:** Handle thousands of simultaneous users
- **System design interviews:** Discuss threading models, async processing, distributed systems
- **Performance optimization:** Choose right concurrency model for the workload
- **Microservices architecture:** Handle inter-service communication efficiently
- **Real-world debugging:** Understand race conditions, deadlocks, performance bottlenecks

---

# 1. Fundamental Concepts

## 1.1 Concurrency vs Parallelism
**Concurrency:** Organizing multiple tasks to make progress together (dealing with many things at once)
**Parallelism:** Actually running multiple tasks simultaneously (doing many things at once)

**Key insight:** Concurrency is about structure, parallelism is about execution
- **Single-core system:** Can have concurrency (time-slicing) but no parallelism
- **Multi-core system:** Can have both concurrency and parallelism

## 1.2 Threading Models
**Multithreading:**
- **Pros:** True parallelism, shared memory access
- **Cons:** Race conditions, complex synchronization, higher memory overhead
- **Best for:** CPU-bound tasks, computational work

**Async/Event-driven:**
- **Pros:** Single thread, no race conditions, memory efficient
- **Cons:** Not suitable for CPU-heavy tasks, callback complexity
- **Best for:** I/O-bound tasks, web servers, network programming

**Rule of thumb for interviews:**
- **I/O-bound workloads** → Async programming (Node.js, Python asyncio)
- **CPU-bound workloads** → Multithreading/multiprocessing (Java threads, Go routines)

---

# 2. Concurrency Models & When to Use Them

## 2.1 Event Loop (Async/Await)
**How it works:** Single thread with non-blocking I/O, event-driven callbacks
**Examples:** Node.js, Python asyncio, JavaScript

**Pros:**
- No race conditions (single thread)
- Memory efficient (no thread overhead)
- Great for I/O-heavy applications

**Cons:**
- CPU-heavy tasks block the entire loop
- Callback complexity ("callback hell")

**Use cases:** Web servers, API gateways, real-time applications

## 2.2 Multithreading
**How it works:** Multiple threads sharing memory, preemptive scheduling
**Examples:** Java threads, .NET threads, C++ std::thread

**Pros:**
- True parallelism on multi-core systems
- Good for CPU-intensive tasks

**Cons:**
- Race conditions and synchronization complexity
- Higher memory overhead (stack per thread)

**Use cases:** CPU-bound processing, parallel algorithms, compute-heavy applications

## 2.3 Actor Model / CSP
**How it works:** Isolated processes communicating via messages
**Examples:** Erlang/Elixir, Akka, Go channels

**Pros:**
- No shared state, reduces bugs
- Fault isolation (one actor failure doesn't affect others)
- Natural distribution across machines

**Cons:**
- Message passing overhead
- More complex for shared data scenarios

**Use cases:** Distributed systems, fault-tolerant applications, messaging systems

**Interview insight:** Choose based on your bottleneck - I/O bound vs CPU bound vs fault tolerance needs

---

# 3. Synchronization & Thread Safety

## 3.1 Common Problems
**Race Condition:** Multiple threads accessing shared data, outcome depends on timing
**Deadlock:** Two threads waiting for each other indefinitely
**Starvation:** Thread can't get resources, indefinitely postponed

## 3.2 Synchronization Primitives
**Mutex/Lock:** Mutual exclusion, only one thread can access critical section
**Semaphore:** Counting lock, allows N threads to access resource
**Read-Write Lock:** Multiple readers OR single writer
**Atomic Operations:** Hardware-level guarantees for simple operations
**Condition Variables:** Wait/notify mechanism for thread coordination

## 3.3 Thread-Safe Strategies
**Lock-based:** Use mutexes, locks (traditional approach)
**Lock-free:** Use atomic operations, compare-and-swap (CAS)
**Immutable data:** Never modify, always create new (functional approach)
**Thread-local storage:** Each thread has its own copy
**Concurrent collections:** Built-in thread-safe data structures

**Senior insight:** Lock-free is faster but harder to implement correctly. Start with locks, optimize later if needed.

---

# 4. Memory Models & Visibility

## 4.1 Memory Model Basics
**Atomicity:** Operation completes fully or not at all
**Visibility:** When changes made by one thread become visible to others
**Ordering:** Prevents compiler/CPU from reordering instructions

## 4.2 Happens-Before Relationship
Key rule: If A happens-before B, then A's effects are visible to B
- Thread start/join operations
- Lock acquire/release operations
- Volatile variable operations
- Final field initialization

## 4.3 Common Issues
**Volatile keyword:** Ensures visibility and prevents reordering (not atomicity)
**False sharing:** Different variables in same cache line cause performance issues
**Memory fences:** Explicit synchronization barriers

**Interview tip:** Know the difference between volatile and synchronized in Java, or equivalent in your language

---

# 5. Async Programming Patterns

## 5.1 Async/Await Pattern
**Benefits:**
- Write asynchronous code that looks synchronous
- No callback nesting
- Exception handling works naturally

**Example flow:** Async functions chain operations naturally without blocking

## 5.2 Reactive Programming
**Stream-based processing:** Treat data as continuous streams
**Operators:** map, filter, merge, combine streams
**Backpressure:** Handle fast producers, slow consumers

**Use cases:** Real-time data processing, event streams, UI interactions

## 5.3 Future/Promise Patterns
**Future/Promise:** Placeholder for a value that will be available later
**Composition:** Chain operations, handle errors, combine multiple futures
**Cancellation:** Ability to cancel in-progress operations

**Senior insight:** Modern applications use structured concurrency to prevent task leaks and ensure proper cleanup

---

# 6. Performance Optimization

## 6.1 Common Bottlenecks
**Context switching:** OS switching between threads (expensive)
**Lock contention:** Multiple threads waiting for the same lock
**False sharing:** Cache line bouncing between CPU cores
**Memory allocation:** Frequent allocation/deallocation overhead

## 6.2 Optimization Strategies
**Thread pools:** Reuse threads instead of creating new ones
**Lock-free algorithms:** Use atomic operations where possible
**Work-stealing queues:** Balance load across threads
**Batch processing:** Process multiple items together
**CPU affinity:** Pin threads to specific cores

## 6.3 Monitoring & Profiling
**Key metrics:** Thread count, lock contention, CPU utilization per core
**Tools:** JProfiler, async profilers, OS tools (top, htop)
**Bottleneck identification:** CPU vs I/O vs memory vs lock contention

**Interview insight:** Always measure before optimizing. Premature optimization is the root of all evil.

---

# 7. Real-World Applications & System Design

## 7.1 Web Server Architecture
**Traditional:** Thread-per-request (Apache)
**Modern:** Event loop (Nginx, Node.js) or virtual threads (Java Loom)
**Hybrid:** Worker pools for CPU tasks, async for I/O

## 7.2 Microservices Communication
**Async messaging:** Kafka, RabbitMQ for decoupling services
**Circuit breakers:** Prevent cascade failures in distributed systems
**Timeout patterns:** Every external call needs timeouts

## 7.3 Database Connection Patterns
**Connection pooling:** Reuse database connections
**Read replicas:** Distribute read load across multiple database instances
**Async database drivers:** Non-blocking database operations

## 7.4 Caching Strategies
**Thread-safe caches:** ConcurrentHashMap, Redis for distributed caching
**Cache-aside pattern:** Application manages cache explicitly
**Write-through/write-behind:** Different consistency guarantees
**System design connection:** Links to [Database patterns](databases.md) and [Redis caching](../3-Backend-Technology-Stack/redis.md)

---

# 📚 Essential Resources & Quick Reference

## Books & Materials
- [Java Concurrency in Practice](../3-Backend-Technology-Stack/pdf/Java-concurrency-in-practice.pdf) - **THE concurrency book**
- [Operating Systems: Three Easy Pieces](pdf/Operating-Systems-Three-Easy-Pieces.pdf) - OS-level concurrency

## Interview Quick Reference
**System design applications:**
- Discuss threading model for your web server design
- Explain how to handle concurrent database connections
- Design async processing pipelines for microservices

**Code interview patterns:**
- Producer-consumer problems (bounded queues)
- Thread pool implementations
- Rate limiters and circuit breakers

**Key discussion points:**
- Trade-offs between different concurrency models
- How to debug race conditions and deadlocks
- Performance characteristics and bottlenecks
- Failure handling and graceful degradation

**Connection to other topics:**
- [Networking](networking.md): Async I/O, connection pooling
- [Databases](databases.md): Transaction isolation, connection management
- [Java](../3-Backend-Technology-Stack/java.md): Executor framework, concurrent collections  
