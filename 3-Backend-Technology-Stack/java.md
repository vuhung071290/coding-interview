# Java for Senior Backend Engineers

> Essential Java knowledge for backend development and technical interviews

## 1. JVM Mastery & Performance

### 1.1 JVM Architecture
- **ClassLoader:** Load → Verify → Prepare → Resolve → Initialize
- **Runtime Areas:** Heap (shared), Stack (per-thread), Method Area/Metaspace
- **Execution Engine:** Interpreter → JIT Compiler (C1/C2) → Optimized machine code
- **TLAB:** Thread Local Allocation Buffer for fast object allocation

### 1.2 Memory Areas
- **Heap:** Objects and arrays, GC-managed, shared across threads
- **Stack:** Method frames, local variables — thread-local, fast
- **Metaspace:** Class metadata (replaced PermGen in Java 8+)

### 1.3 GC Algorithms

| GC | Characteristics | Use When |
|----|----------------|----------|
| Serial GC | Single-threaded | Small apps, dev |
| Parallel GC | Throughput-focused | Batch processing |
| G1 GC | Low pause (~100ms), large heaps (default Java 9+) | Web applications |
| ZGC | Sub-millisecond pauses (Java 21: generational ZGC) | Real-time, latency-critical |
| Shenandoah | Concurrent, very low latency | Low-latency production |

**Production settings:** `-Xms = -Xmx` (avoid heap resizing overhead)

### 1.4 Memory Troubleshooting

**OutOfMemoryError types:**
- `Heap space` — most common → increase heap or fix leaks
- `Metaspace` — too many classes → framework/classloader issue
- `Direct buffer memory` — NIO/Netty off-heap exhaustion

**Common leak sources:** static collections, ThreadLocal cleanup missed in pools, event listeners, classloader leaks in app server redeployment

**Tools:** JProfiler, VisualVM, heap dumps (`jmap -dump`), async profilers (async-profiler, Flame graphs)

### 1.5 JVM Troubleshooting Commands
- `jps` — list JVM processes
- `jstack <pid>` — thread dump (deadlock detection)
- `jmap -histo <pid>` — heap histogram
- `jcmd <pid> GC.run` — force GC
- `jstat -gc <pid>` — GC stats live

### 1.6 JVM Tuning Flags & Capacity Planning
- **GC selection:** G1 (low pause ~100ms), ZGC/Shenandoah cho low-latency (ZGC production-ready Java 21)
- **Container awareness:** Java 17+ auto-detect cgroup limits; `-XX:MaxRAMPercentage=75.0` khi set `-Xms=-Xmx`
- **Heap sizing:** `-Xms = -Xmx` (tránh resize overhead); heap ≤ container memory limit
- **Capacity planning:** estimate heap từ container limit × 0.75; metaspace cho loaded classes; direct buffers cho Netty/NIO off-heap
- **Sizing checks:** heap dump khi OOM; jstat/jcmd để monitor GC pause

---

## 2. Java Core

### 2.1 OOP Principles
- **Encapsulation:** hide internals, expose clean interface
- **Abstraction:** what it does, not how
- **Inheritance:** is-a relationships, code reuse
- **Polymorphism:** one interface, multiple implementations

**Method dispatch:**
- **Overloading:** compile-time (parameter types)
- **Overriding:** runtime (actual object type — dynamic dispatch)

### 2.2 Collections Framework

| Type | Implementation | Complexity | Notes |
|------|---------------|------------|-------|
| List | ArrayList | O(1) get, O(n) insert | Dynamic array |
| List | LinkedList | O(n) get, O(1) insert | Doubly-linked |
| Set | HashSet | O(1) avg | Hash table |
| Set | TreeSet | O(log n) | Red-black tree, sorted |
| Map | HashMap | O(1) avg | Hash table |
| Map | TreeMap | O(log n) | Red-black tree, sorted |
| Map | LinkedHashMap | O(1) avg | Insertion order |

**HashMap internals:** array of buckets → linked list/tree (Java 8+: treeify when >8 entries). Resize at 75% load factor.

### 2.3 equals() and hashCode() contract
- If `a.equals(b)` → `a.hashCode() == b.hashCode()`
- Override **both** together — breaking this breaks HashMap/HashSet correctness

### 2.4 String
- **Immutable:** security, thread safety, string pool optimization
- **String pool:** literals cached for memory efficiency
- **StringBuilder:** mutable, use for concatenation in loops (not thread-safe)
- **StringBuffer:** thread-safe StringBuilder (rarely needed)

---

## 3. Java Concurrency

### Thread Safety Mechanisms
- **volatile:** memory visibility + reordering prevention — NOT atomic
- **synchronized:** mutual exclusion + memory sync (reentrant)
- **Atomic classes:** lock-free thread-safe ops (AtomicInteger, AtomicReference, etc.)
- **ReentrantLock:** flexible locking — tryLock, timed lock, fairness
- **ReadWriteLock:** multiple readers OR exclusive writer
- **StampedLock:** optimistic reads for high-performance

### Executors Framework
- `FixedThreadPool(n)` — bounded, predictable
- `CachedThreadPool` — unbounded, risk OOM under load
- `ScheduledExecutorService` — periodic tasks
- `VirtualThreadPerTaskExecutor` — Java 21, virtual threads per task

### Concurrent Collections
- `ConcurrentHashMap` — O(1), segment-level locking → better than Hashtable
- `CopyOnWriteArrayList` — O(n) writes, O(1) reads — read-heavy scenarios
- `BlockingQueue` variants — producer-consumer patterns, backpressure

### Common Pitfalls
- Race conditions: multiple threads modifying shared state
- Deadlock: lock ordering violations (always lock in consistent order)
- ThreadLocal leaks in thread pools: always clean up in `finally`

> Virtual Threads → see [concurrent.md](../1-Computer-Science/concurrent.md)

---

## 4. Modern Java — Features 8 to 21

### Java 8
- **Lambda expressions:** `(x) -> x * 2` — replace anonymous classes
- **Stream API:** functional-style data processing — filter, map, collect, reduce
- **Optional:** null-safe programming — avoid NPE, explicit optionality
- **Method references:** `String::length`, `System.out::println`
- **CompletableFuture:** async programming with method chaining

### Java 9–15 (key features)
- **Modules (9):** modular JDK, explicit dependencies
- **var (10):** local variable type inference
- **Switch expressions (14):** returns values, arrow syntax
- **Text Blocks (15):** multi-line strings with `"""`

### Java 16–21 (LTS — Key for Interviews)

#### Records (16+)
- Immutable data carrier — auto-generates constructor, getters, equals/hashCode/toString
- Use for: DTOs, value objects, response/request types
- Compact constructor available for validation

#### Sealed Classes (17+)
- Define **closed hierarchy** — compiler knows all subtypes
- Used with pattern matching switch — exhaustive (no default needed)
- Use for: domain algebraic types, payment result types (Success/Failure/Pending)

#### Pattern Matching (16–21)
- `instanceof` without cast: `if (obj instanceof String s)`
- Switch patterns: match on type + deconstruct — exhaustive checking with sealed classes

#### Virtual Threads / Project Loom (21)
- Lightweight JVM-managed threads — millions possible
- Simple blocking code với scalability của reactive
- Spring Boot 3.2+: `spring.threads.virtual.enabled=true`
- → Deep dive: [concurrent.md](../1-Computer-Science/concurrent.md)

#### Structured Concurrency (21)
- Parallel tasks với automatic cancellation if any fail
- `StructuredTaskScope.ShutdownOnFailure` / `ShutdownOnSuccess`

#### Sequenced Collections (21)
- `getFirst()`, `getLast()`, `reversed()` on List/Deque — O(1) on ArrayList

#### ZGC Generational (21)
- Generational ZGC → production-ready, sub-millisecond pauses

---

## 5. Best Practices & Security

### Code Quality
- Prefer immutable objects; `final` fields, Collections.unmodifiableXxx
- Use try-with-resources for all Closeable resources
- Never swallow exceptions silently — log at minimum
- Choose data structures by access patterns, not convenience

### Security Essentials
- Hashing: SHA-256+ (avoid MD5, SHA-1)
- Encryption: AES (avoid DES)
- Use `SecureRandom` for cryptographic randomness
- **Never** implement own crypto — use proven libraries (Bouncy Castle, etc.)
- Validate all external input, sanitize output

---

## 📚 Essential Resources

- **Effective Java** (3rd ed.) — THE Java best practices book
- **Java Concurrency in Practice** — Essential for concurrent programming
- **Java Performance: The Definitive Guide** — Production optimization

### Common Interview Questions
- "Explain how HashMap works internally" → hash table, bucket, treeify, resize
- "What's the difference between synchronized and volatile?" → visibility+atomicity vs visibility only
- "How do you handle memory leaks in Java?" → profilers, heap dumps, common sources
- "Design a thread-safe singleton" → enum singleton or double-checked locking with volatile
- "Explain GC and when to tune it" → GC algorithm trade-offs, pause vs throughput
- "Records vs regular classes?" → Immutable, boilerplate-free value objects
- "Virtual Threads vs WebFlux?" → Virtual Threads for most I/O-bound; WebFlux for streaming/SSE
