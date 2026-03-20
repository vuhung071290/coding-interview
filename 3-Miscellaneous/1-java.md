# Java – Senior Backend Interview Notes

---

# 1. JVM & Memory Management

## 1.1 JVM Architecture
- ClassLoader: load → verify → prepare → resolve → initialize
- Runtime Areas: Heap, Stack, Method Area, PC Register, Native Stack
- Execution Engine: Interpreter, JIT (C1/C2), HotSpot optimizations
- TLAB (Thread Local Allocation Buffer) giúp allocate object cực nhanh.

## 1.2 Java Memory Model (JMM)
- Đảm bảo visibility & ordering.
- Happens-before rules:
  - unlock → lock
  - volatile write → volatile read
  - thread start/join
  - final field guarantee (immutable safety)

## 1.3 Heap vs Stack
- Stack: method frames, primitives, references → thread-local.
- Heap: shared objects, cần GC.

## 1.4 Garbage Collection
- Mark → Sweep → Compact
- Generational: Young (Eden/S0/S1) → Old
- GC types:
  - Parallel GC → throughput
  - G1 GC → low pause, region-based
  - ZGC, Shenandoah → ultra-low pause (<1–10ms)

## 1.5 References
- Strong, Soft (cache), Weak (WeakHashMap), Phantom (cleanup).

## 1.6 Class Loading
- Parent Delegation Model.
- Lỗi: ClassNotFoundException, NoClassDefFoundError.

## 1.7 Common JVM Issues
- OOM: heap, metaspace, direct buffer.
- Memory leaks: static references, ThreadLocal, caches, classloader.

---

# 2. Java Core

## 2.1 OOP Essentials
- Encapsulation, Abstraction, Inheritance, Polymorphism.
- Overloading (compile-time) vs Overriding (runtime, dynamic dispatch).

## 2.2 Important Keywords
- final (var/method/class), finally, finalize (deprecated).
- this, super
- static fields & methods
- pass-by-value (tham chiếu bản copy)

## 2.3 String Internals
- String immutable (security + caching + threading).
- StringBuilder (non-thread-safe) vs StringBuffer (thread-safe).
- String Pool.

## 2.4 equals() & hashCode()
- Contract: equal objects must have same hash.
- Hash structures (HashMap, HashSet) phụ thuộc hashCode.

## 2.5 Object Model
- shallow copy vs deep copy
- clone() rarely recommended → prefer copy constructor

## 2.6 Autoboxing
- int → Integer automatic.
- Pitfall: NullPointerException khi unboxing null.

---

# 3. Concurrency & Multithreading

## 3.1 Thread Basics
- Thread lifecycle: NEW → RUNNABLE → BLOCKED/WAITING → TERMINATED.
- Thread.sleep() vs wait().

## 3.2 Synchronization
- synchronized: reentrant lock, visibility + atomicity.
- volatile: visibility + prevent reordering (không atomic).

## 3.3 Locks
- ReentrantLock, ReadWriteLock
- Condition vs Object.wait()
- StampedLock (Java 8+): optimistic read.

## 3.4 High-level Concurrency
- Executors: fixed, cached, scheduled thread pools
- Futures & CompletableFuture (async pipelines)

## 3.5 Concurrent Collections
- ConcurrentHashMap: segmented → CAS-based bins
- CopyOnWriteArrayList: read-heavy list
- BlockingQueue: Synchronous, LinkedBlocking, ArrayBlocking

## 3.6 ThreadLocal
- Gắn data theo thread (context, auth, formatting).
- Dễ gây memory leak nếu dùng trong thread pool.

## 3.7 Common Pitfalls
- deadlock, livelock, starvation
- visibility issues, race condition
- double-checked locking (fixed since JDK5 with volatile)

---

# 4. Collections Framework

## 4.1 List / Set / Map
- ArrayList: random access O(1)
- LinkedList: insert/remove O(1)
- HashSet: backed by HashMap
- TreeSet/TreeMap: sorted (Red-Black tree)

## 4.2 HashMap Internals
- hash spreader → bucket → linked list → treeify
- Threshold: treeify when bucket size ≥ 8
- Load factor default: 0.75
- Resize cost: O(n)

## 4.3 Fail-fast vs Fail-safe
- Fail-fast: ArrayList, HashMap (ConcurrentModificationException)
- Fail-safe: CopyOnWriteArrayList, ConcurrentHashMap.

## 4.4 Sorting
- Comparable vs Comparator
- Arrays.sort() dùng Dual-Pivot Quicksort (primitive) và TimSort (object).

---

# 5. Exception Handling

## 5.1 Types
- Checked (IOException)
- Unchecked (NullPointerException)

## 5.2 Best Practices
- Không catch chung Exception
- Không swallow exception
- Sử dụng try-with-resources cho AutoCloseable

---

# 6. Java 8+ Modern Features

## 6.1 Functional Interfaces
- Runnable, Callable, Supplier, Consumer, Predicate, Function.

## 6.2 Lambda & Method Reference
- Cleaner, concise, less boilerplate.

## 6.3 Stream API
- Intermediate: filter, map, flatMap, sorted…
- Terminal: collect, reduce, count, anyMatch…
- Parallel stream: cẩn thận với shared state.

## 6.4 Optional
- Giảm null checks
- Không dùng Optional trong fields.

## 6.5 CompletableFuture
- supplyAsync, thenApply, thenCompose, allOf…

---

# 7. Design Patterns (Java-focused)

## 7.1 Important Patterns
- Singleton (thread-safe)
- Factory / Abstract Factory
- Builder (immutable objects)
- Strategy (algorithm switching)
- Observer (event-based)
- Decorator (extend behavior)
- Template Method

## 7.2 SOLID
- Single Responsibility
- Open/Closed
- Liskov
- Interface segregation
- Dependency inversion

## 7.3 Dependency Injection
- Constructor injection > setter injection
- Foundation for Spring.

---

# 8. I/O & NIO

## 8.1 IO (Blocking)
- Streams: InputStream / OutputStream
- Readers/Writers (text)

## 8.2 NIO (Non-blocking)
- Channels, Buffers, Selectors
- DirectByteBuffer (off-heap)
- Memory-mapped file: MappedByteBuffer

---

# 9. Java Security (Basic for Backend)
- JCA: MessageDigest, Cipher, KeyFactory
- SHA-256 hashing
- AES symmetric encryption
- SecureRandom
- Avoid: MD5, SHA-1, homemade crypto.

---

# 10. Testing
- JUnit basics
- Mockito:
  - mock(), when(), verify()
- TDD mindset
- Integration test vs unit test

---

# 11. Build Tools
- Maven: pom.xml, lifecycle, dependency scopes
- Gradle: Groovy/Kotlin DSL, faster builds
- Multi-module setup

---

# 12. Tổng kết Roadmap Java (Senior-ready)
1. JVM + Memory + GC
2. Core Java (OOP, String, Object model)
3. Concurrency + JMM + Executor
4. Collections (đặc biệt HashMap)
5. Exceptions
6. Java 8+ (Streams, Optional, Lambda)
7. Design Patterns + SOLID
8. I/O + NIO
9. Security basics
10. Testing, Mockito
11. Maven/Gradle

# Reference Materials

## Books
- [Effective Java](pdf/Effective-Java-3rd-Edition.pdf)
- [Java The Complete Reference](pdf/Java-The-Complete-Reference-11-Edition.pdf)
- [Java concurrency in practice](pdf/Java-concurrency-in-practice.pdf)
- [Java Performance The Definitive Guide](pdf/Java-Performance-The-Definitive-Guide.pdf)
- [Java interview notes](pdf/Java-interview-notes-covers-136-topics-and-700-technical-interview-questions.pdf)
- [Grokking the java interview](pdf/Grokking-the-java-interview.pdf)
- [Grokking the java developer interview](pdf/Grokking-the-java-developer-interview.pdf)