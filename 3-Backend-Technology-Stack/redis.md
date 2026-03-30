# Redis for Backend Engineers

> In-memory data structure store for high-performance caching, sessions, and real-time applications

## 🎯 Why Redis Matters for Backend Engineers
- **Performance optimization:** Sub-millisecond response times, 100K+ ops/sec
- **System design interviews:** Essential for caching layers, session stores, rate limiters
- **Scalability patterns:** Distributed caching, session clustering, pub/sub messaging
- **Production reliability:** Battle-tested in high-traffic systems (Twitter, GitHub, Instagram)
- **Versatile data structures:** Strings, hashes, lists, sets, sorted sets, streams, bitmaps
- **Real-time features:** Pub/sub, streams, distributed locks, leaderboards

**Focus:** Production Redis patterns for scalable backend systems and system design interviews.

---

## 1. Redis Overview
- In-memory data structure store → cực nhanh (single-thread, event loop).
- Use cases: cache, session, rate-limiter, leaderboard, distributed lock, pub/sub, streams.
- Ưu điểm: low latency, đa cấu trúc dữ liệu, replication/HA/clustering tốt.

---

## 2. Architecture Overview

### 2.1 Single Instance
- Đơn giản, không HA.
- Dùng cho dev/test hoặc cache không quan trọng.

### 2.2 Replication (Primary–Replica)
- Async replication.
- Key concepts:
  - replication ID
  - replication offset
  - full sync / partial sync
  - backlog buffer
- Replica read-only (mặc định).

### 2.3 Sentinel (HA)
- Monitor + auto-failover.
- Quorum để hạn chế split-brain.
- Cung cấp service discovery.
- Production: tối thiểu 3 Sentinel.

### 2.4 Redis Cluster (Sharding)
- 16384 hash slots.
- Masters & replicas.
- Gossip protocol.
- Auto failover.
- Hạn chế: multi-key khác hash slot không hoạt động (trừ hash tag).

---

## 3. Persistence (RDB / AOF)

### 3.1 RDB
- Snapshot định kỳ.
- fork + COW.
- Ưu: restart nhanh, file nhỏ.
- Nhược: mất data giữa snapshots.

### 3.2 AOF
- Ghi write commands.
- fsync: always / everysec / no.
- Ưu: durable; Nhược: file lớn.
- AOF rewrite để nén.

### 3.3 Kết hợp RDB + AOF
- Best practice production.

---

## 4. Data Structures & Internal

### 4.1 Cấu trúc dữ liệu chính
- String, Hash, List, Set, Sorted Set.
- HyperLogLog → approximate cardinality.
- Bitmap/Bitfield → tracking, flags.
- Streams → persistent message log.

### 4.2 Internal Encoding
- embstr/raw
- listpack/ziplist
- hashtable
- skiplist for ZSET

### 4.3 Threading Model
- Command execution: single-thread.
- Redis 6+: I/O threads giảm latency network.

---

## 5. Eviction Policies
- noeviction
- allkeys-lru / allkeys-lfu
- volatile-lru / volatile-lfu
- allkeys-random
- volatile-random
- volatile-ttl

Production: **allkeys-lru** hoặc **allkeys-lfu**.

---

## 6. Transactions & Lua

### 6.1 MULTI/EXEC
- Không rollback.
- WATCH = optimistic locking.

### 6.2 Lua
- Atomic toàn bộ.
- EVAL vs EVALSHA.
- Dùng cho lock logic, batch ops.

---

## 7. Distributed Locking

### 7.1 Simple Lock
- `SET key value NX PX ttl`
- value = unique token để unlock an toàn.

### 7.2 RedLock
- Lock đa node.
- An toàn có điều kiện (phụ thuộc latency, clock drift).

---

## 8. Messaging

### 8.1 Pub/Sub
- Real-time push.
- Không lưu message → không đảm bảo delivery.

### 8.2 Redis Streams
- Persistent log.
- Ordering theo stream ID.
- Consumer groups (giống Kafka groups).
- PEL → retry được.

---

## 9. Memory Management
- Fragmentation (jemalloc).
- Big keys gây:
  - block event loop → latency spikes
  - replication chậm
  - cluster rebalance chậm
- Optimization:
  - key ngắn
  - dùng hash thay nhiều keys nhỏ

---

## 10. Monitoring
- slowlog
- big key scan
- INFO memory, stats, clients
- replication lag
- cluster slot health, failover state

---

## 11. Client-side Sharding
- Consistent hashing (Twemproxy, Codis).
- Ưu: dễ quản lý.
- Nhược: không auto-failover, không có gossip metadata.

---

# Top Interview Topics (Senior Level)

---

## A. Core Concepts
- Tại sao Redis nhanh? (event loop + in-memory + async I/O + compact structures)
- Redis vs Memcached.
- Ảnh hưởng page fault.

---

## B. Data Structures
- Sorted Set dùng skiplist → O(log n).
- HyperLogLog cho estimate unique.
- Streams vs Pub/Sub → persistence + replay.

---

## C. Persistence
- RDB vs AOF trade-offs.
- fork + COW và memory spike.
- AOF rewrite mechanics.

---

## D. Replication
- Full sync / partial sync.
- backlog buffer.
- replication offset.

---

## E. Sentinel
- Failover steps.
- Quorum & majority.
- Split-brain scenarios.

---

## F. Cluster
- 16384 slots design.
- Gossip protocol.
- Multi-key limitations.

---

## G. Eviction & Memory
- LRU vs LFU.
- Big key anti-patterns.

---

## H. Transactions & Lua
- WATCH → optimistic lock.
- Lua atomic logic.

---

## I. Distributed Lock
- SET NX PX + unique token.
- RedLock constraints.

---

## J. Performance Optimization
- Pipelining.
- Tránh big key.
- Hashes for memory efficiency.
- Slowlog analysis.

---

## K. Deployment Strategy
- Single → dev.
- Replication → read scaling.
- Sentinel → HA.
- Cluster → sharding & large scale.

---

# 📚 Essential Resources & System Design Applications

## Interview Quick Reference
**Core concepts (must know):**
- Data structures and their use cases
- Memory management and eviction policies
- Persistence options (RDB vs AOF)
- High availability patterns (Sentinel, Cluster)
- Performance characteristics and limitations

**System design applications:**
- **Caching layer:** Database query caching, application-level cache
- **Session storage:** Distributed session management for stateless apps
- **Rate limiting:** Token bucket, sliding window implementations
- **Real-time features:** Leaderboards, counters, real-time analytics
- **Distributed locks:** Coordination in distributed systems

**Common interview questions:**
- "Design a distributed cache system"
- "How would you implement a rate limiter using Redis?"
- "Explain Redis clustering and how it handles failover"
- "Design a real-time leaderboard system"
- "How do you prevent cache stampede?"

## Performance Benchmarks
**Typical performance:**
- Single instance: 100K+ operations/second
- Memory access: <1ms response times
- Network latency: Primary bottleneck in production
- Cluster throughput: Scales linearly with nodes

## Production Patterns
**Caching strategies:**
- **Cache-aside:** Application manages cache
- **Write-through:** Synchronous cache updates
- **Write-behind:** Asynchronous cache updates
- **Refresh-ahead:** Proactive cache warming

**High availability:**
- **Master-slave replication:** For read scaling
- **Sentinel:** Automatic failover and monitoring
- **Redis Cluster:** Horizontal scaling and partitioning

## Best Practices Summary
**Memory optimization:**
- Use appropriate data structures for use case
- Avoid large keys (>10MB)
- Configure eviction policies based on workload
- Monitor memory usage and fragmentation

**Operations:**
- Enable persistence for important data
- Set appropriate timeouts for connections
- Use pipelining for bulk operations
- Monitor slow log for performance issues


