# Java for Senior Backend Engineers

> Essential Java knowledge for backend development and technical interviews

## 🎯 Why Java Expertise Matters for Backend Engineers
- **Production systems:** Most enterprise backends run on JVM (Java, Kotlin, Scala)
- **Performance optimization:** JVM tuning, garbage collection, memory management
- **Concurrency mastery:** Handle high-traffic systems with thread safety
- **Framework foundation:** Spring, microservices, enterprise patterns built on Java
- **Interview advantage:** Deep JVM knowledge sets senior engineers apart
- **System design:** Memory models, performance characteristics affect architecture decisions

**Focus:** Production-ready Java knowledge that directly applies to backend systems and interviews.

---

# 1. JVM Mastery & Performance

## 1.1 JVM Architecture & Runtime
**JVM Components:**
- **ClassLoader:** Load → Verify → Prepare → Resolve → Initialize
- **Runtime Areas:** Heap (shared), Stack (per-thread), Method Area/Metaspace
- **Execution Engine:** Interpreter → JIT Compiler (C1/C2) → Optimized machine code
- **TLAB:** Thread Local Allocation Buffer for fast object allocation

**Memory Model:**
- **Heap:** Objects and arrays, managed by GC, shared across threads
- **Stack:** Method frames, local variables, thread-local and fast
- **Method Area:** Class metadata, constant pool (Metaspace in Java 8+)

## 1.2 Java Memory Model (JMM) & Thread Safety
**Happens-before relationships:**
- **Monitor operations:** unlock happens-before subsequent lock
- **Volatile variables:** write happens-before subsequent read
- **Thread operations:** start/join operations establish ordering
- **Final fields:** initialization happens-before any read

**Practical implications:**
- **volatile:** Ensures visibility across threads, prevents reordering
- **synchronized:** Provides mutual exclusion and memory synchronization
- **Atomic classes:** Lock-free thread-safe operations

**Interview insight:** JMM is crucial for understanding concurrent programming bugs and solutions

## 1.3 GC Quick Reference for Backend Engineers
• **Parallel GC** - Default, Moderate pause times → Batch processing
• **G1 GC** - Large heaps (4GB+), Low pause (~100ms) → Web applications
• **ZGC/Shenandoah** - Ultra-low latency (<10ms) → Real-time systems

**Production Settings:** `-Xms = -Xmx` (avoid heap resizing overhead)

## 1.4 Memory Troubleshooting Checklist
**OutOfMemoryError Types:**
- 🔥 **Heap space:** Most common → Increase heap or fix leaks
- ⚡ **Metaspace:** Too many classes → Framework issue
- 💾 **Direct buffer:** Off-heap exhaustion → NIO/Netty

**Common Memory Leak Sources:**
- Static collections, ThreadLocal cleanup, Event listeners
- **Classloader leaks:** In application servers during redeployment

**Senior insight:** Use profilers (JProfiler, VisualVM) and heap dumps for memory analysis

---

# 2. Java Core & Object-Oriented Design

## 2.1 OOP Principles & Polymorphism
**Four pillars:**
- **Encapsulation:** Hide internal details, expose clean interfaces
- **Abstraction:** Focus on what an object does, not how
- **Inheritance:** Code reuse and establishing "is-a" relationships
- **Polymorphism:** One interface, multiple implementations

**Method dispatch:**
- **Overloading:** Compile-time resolution based on parameters
- **Overriding:** Runtime resolution based on actual object type
- **Dynamic dispatch:** JVM chooses method based on object's actual class

## 2.2 Essential Java Features
**String handling:**
- **Immutability:** Security, thread safety, string pool optimization
- **StringBuilder:** Mutable, efficient for concatenation (not thread-safe)
- **String pool:** Literal strings cached for memory efficiency

**equals() and hashCode() contract:**
- If `a.equals(b)` is true, then `a.hashCode() == b.hashCode()`
- Override both together for HashMap/HashSet correctness
- Common bug: forgetting to override hashCode when overriding equals

**Object lifecycle:**
- **Constructors:** Initialize object state properly
- **Garbage collection:** Automatic memory management, no explicit destructors
- **finalize():** Deprecated, unreliable cleanup mechanism

## 2.3 Collections Framework & Generics
**Core interfaces:**
- **List:** ArrayList (dynamic array), LinkedList (doubly-linked)
- **Set:** HashSet (hash table), TreeSet (red-black tree)
- **Map:** HashMap (hash table), TreeMap (red-black tree), LinkedHashMap (insertion order)

**Performance characteristics:**
- **ArrayList:** O(1) access, O(n) insertion/deletion (middle)
- **HashMap:** O(1) average get/put, O(n) worst case
- **TreeMap/TreeSet:** O(log n) operations, sorted order

**Generics benefits:**
- **Type safety:** Compile-time error detection
- **Performance:** Eliminates casting overhead
- **Code clarity:** Self-documenting interfaces

**Senior insight:** Choose data structure based on access patterns, not just convenience

---

# 3. Java Concurrency for Backend Systems

## 3.1 Thread Safety Fundamentals
**Thread lifecycle:** NEW → RUNNABLE → BLOCKED/WAITING/TIMED_WAITING → TERMINATED

**Synchronization mechanisms:**
- **synchronized:** Mutual exclusion + memory visibility (reentrant)
- **volatile:** Memory visibility + prevents reordering (not atomic)
- **Atomic classes:** Lock-free thread-safe operations (AtomicInteger, etc.)

**Thread coordination:**
- **wait()/notify():** Object-level coordination primitive
- **Thread.sleep():** Pause current thread for specified time
- **Thread.join():** Wait for another thread to complete

## 3.2 Advanced Concurrency Tools
**Explicit locks (java.util.concurrent.locks):**
- **ReentrantLock:** More flexible than synchronized
- **ReadWriteLock:** Multiple readers, exclusive writer
- **StampedLock:** Optimistic reads for high-performance scenarios

**Executors Framework:**
- **FixedThreadPool:** Fixed number of threads
- **CachedThreadPool:** Creates threads as needed
- **ScheduledExecutorService:** Delayed and periodic execution
- **CompletableFuture:** Asynchronous programming with pipelines

## 3.3 Concurrent Collections
**Thread-safe collections:**
- **ConcurrentHashMap:** Lock-free segments, CAS-based operations
- **CopyOnWriteArrayList:** Copy-on-write, optimized for reads
- **BlockingQueue variants:** Producer-consumer patterns

**Performance characteristics:**
- **ConcurrentHashMap:** O(1) operations, better than Hashtable
- **CopyOnWriteArrayList:** O(n) writes, O(1) reads
- **LinkedBlockingQueue:** Unbounded, good for producer-consumer

## 3.4 Common Concurrency Issues
**Problems to avoid:**
- **Race conditions:** Multiple threads modifying shared state
- **Deadlock:** Threads waiting for each other indefinitely
- **Memory leaks:** ThreadLocal not cleaned up in thread pools

**Best practices:**
- Prefer immutable objects when possible
- Use thread-safe collections over synchronized wrappers
- Always clean up ThreadLocal in web applications
- Understand happens-before relationships

**Backend relevance:** Links to [Concurrency patterns](../1-Computer-Science/concurrent.md) and distributed system considerations

---

# 4. Modern Java Features & Best Practices

## 4.1 Java 8+ Functional Programming
**Lambda expressions:** Concise function syntax, replace anonymous classes
**Stream API:** Functional-style data processing with filter, map, collect operations
**Method references:** Cleaner lambda syntax với :: operator

## 4.2 Optional & Error Handling
**Optional:** Null-safe programming
- Avoid null pointer exceptions
- Express optionality explicitly
- Chain operations safely

**Exception handling best practices:**
- **Checked exceptions:** IOException, SQLException (must be handled)
- **Unchecked exceptions:** RuntimeException subclasses
- **Try-with-resources:** Automatic resource management
- Never swallow exceptions without logging

## 4.3 CompletableFuture & Async Programming
**CompletableFuture:** Asynchronous programming với method chaining
**Backend applications:** Non-blocking I/O, better resource utilization

# 5. Backend Development & Production Considerations

## 5.1 I/O & Performance
**Traditional I/O (Blocking):**
- InputStream/OutputStream for binary data
- Reader/Writer for character data
- Blocking operations, one thread per connection

**NIO (Non-blocking I/O):**
- Channels, Buffers, Selectors for scalable I/O
- Memory-mapped files for large file processing
- Better for high-concurrency servers

## 5.2 Testing & Code Quality
**Testing fundamentals:**
- **JUnit:** Standard Java testing framework
- **Mockito:** Mock external dependencies (mock(), when(), verify())
- **Test types:** Unit tests vs Integration tests vs End-to-end tests

**Best practices:**
- Test-driven development (TDD) mindset
- Mock external services and databases
- Use dependency injection for testability

## 5.3 Build Tools & Project Management
**Maven vs Gradle:**
- **Maven:** XML-based, convention over configuration, mature ecosystem
- **Gradle:** Groovy/Kotlin DSL, faster builds, more flexible

**Multi-module projects:** Separate concerns, shared dependencies, easier maintenance

## 5.4 Security Essentials
**Java Cryptography Architecture (JCA):**
- MessageDigest for hashing (SHA-256, avoid MD5)
- Cipher for encryption (AES, avoid DES)
- SecureRandom for cryptographically strong randomness

**Security principles:**
- Never implement your own crypto
- Use proven libraries and algorithms
- Validate all input, sanitize output

---

# 6. Interview Preparation & System Design Connections

## 6.1 Core Java Interview Topics
**Must-know concepts:**
- JVM architecture and memory management
- Concurrency: synchronized, volatile, thread safety
- Collections: HashMap internals, concurrent collections
- Exception handling and best practices
- Modern Java features: Streams, Optional, CompletableFuture

## 6.2 System Design Applications
**Java in backend systems:**
- **Microservices:** Spring Boot applications, containerization
- **High-throughput systems:** JVM tuning, concurrent programming
- **Data processing:** Stream processing, batch jobs
- **API development:** RESTful services, async processing

## 6.3 Performance Optimization
**JVM tuning for production:**
- Heap sizing and GC algorithm selection
- Profiling tools: JProfiler, VisualVM, async profilers
- Memory leak detection and resolution
- Thread pool sizing and monitoring

**Code optimization:**
- Choose appropriate data structures
- Minimize object allocation in hot paths
- Use primitive collections for performance-critical code
- Profile before optimizing

---

# 📚 Essential Resources & Quick Reference

## Must-Read Books
- [Effective Java](pdf/Effective-Java-3rd-Edition.pdf) - **THE Java best practices book**
- [Java Concurrency in Practice](pdf/Java-concurrency-in-practice.pdf) - Essential for concurrent programming
- [Java Performance: The Definitive Guide](pdf/Java-Performance-The-Definitive-Guide.pdf) - Production optimization

## Interview Quick Reference
**System design relevance:**
- JVM memory model for caching strategies
- Concurrency patterns for scalable systems
- Collection performance characteristics for data structure choices
- Async programming for non-blocking architectures

**Common interview questions:**
- "Explain how HashMap works internally"
- "What's the difference between synchronized and volatile?"
- "How do you handle memory leaks in Java applications?"
- "Design a thread-safe singleton"
- "Explain garbage collection and when to tune it"

