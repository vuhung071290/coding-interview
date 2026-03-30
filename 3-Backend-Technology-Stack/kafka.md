# Apache Kafka for Backend Engineers

> Distributed event streaming platform for scalable, real-time data processing

## 🎯 Why Kafka Matters for Backend Engineers
- **Event-driven architectures:** Decouple microservices with reliable messaging
- **System design interviews:** Essential component for scalable, real-time systems
- **High-throughput systems:** Handle millions of messages per second
- **Data integration:** Connect databases, APIs, analytics systems, ML pipelines
- **Fault tolerance:** Built-in replication, durability, high availability
- **Real-world usage:** Used by 80% of Fortune 100 companies (LinkedIn, Netflix, Uber, Airbnb)

**Focus:** Production-ready Kafka knowledge for backend systems and system design interviews.

---

## 1. Kafka Overview & Core Concepts
- Distributed event streaming platform.
- Components:
  - **Topic** → logical stream.
  - **Partition** → unit of parallelism; ordered log.
  - **Offset** → position in partition.
  - **Broker** → stores partitions.
  - **Cluster** → multiple brokers.
- Key properties: high throughput, durability, scalability, replayable logs.

---

## 2. Kafka Architecture
- **Leader/Follower replication**: Producers & consumers only talk to leader.
- **Replication factor (RF)** = durability; RF=3 common.
- **ISR (In-Sync Replicas)**: followers fully caught up.
- High availability via leader election.

---

## 3. Log Storage Internals
- Partition = append-only log → split into **segments**.
- Each segment: `.log`, `.index`, `.timeindex`.
- **Retention**:
  - Time-based: `retention.ms`
  - Size-based: `retention.bytes`
  - Delete vs Compact.
- Tiered storage (optional knowledge): cold storage for old segments.

---

## 4. Log Compaction
- Keep the **latest record per key**.
- Useful for compacted topics: user profile, configs.
- Ensures “latest state” instead of event history.
- Main flags: `cleanup.policy=compact`.

---

## 5. Producer Deep Dive
- **acks=0/1/all** → durability.
- **Idempotence** ON by default: avoids duplicates.
- Batching: `linger.ms`, `batch.size`.
- Compression: snappy, zstd.
- Partitioning strategies: round-robin, key-based.

---

## 6. Consumer Deep Dive
- Pull model → consumer controls fetch.
- `max.poll.interval.ms` → controls processing timeout.
- `max.poll.records` → batch size.
- Offset commit: auto vs manual.
- Strategies: earliest, latest, none.

---

## 7. Consumer Group & Rebalance
- Group ensures load balancing.
- One partition → only consumed by one consumer in group.
- Rebalance triggers:
  - New consumer joins
  - Consumer leaves/crashes
  - Partition count changes
- Rebalance protocols:
  - Range, RoundRobin
  - Cooperative Sticky Rebalance (preferred)

---

## 8. Delivery Semantics
- **At-most-once** → commit before processing.
- **At-least-once** → commit after processing (default).
- **Exactly-once**:
  - Producer idempotence
  - Transactions + Kafka Streams support.

---

## 9. Idempotence & Transactions
- Idempotent producer:
  - Avoid duplicates within same session.
  - Uses sequence numbers (PID + sequence).
- Transactions:
  - Atomic write across partitions/topics.
  - Prevents partial results ("exactly once").

---

## 10. Kafka Streams
- Library for building stream processing apps.
- **KStream** (events) vs **KTable** (state).
- Stateful processing: RocksDB state store.
- Repartition topics used when grouping/joining.
- Windowing: tumbling, hopping, sliding.

---

## 11. Kafka Connect
- Integration/ETL layer.
- **Source connector** → external → Kafka.
- **Sink connector** → Kafka → external.
- **SMT (Single Message Transforms)**: lightweight per-record transforms.
- Scaling: distributed workers.

---

## 12. Serialization & Schema Registry
- Common formats: JSON, Avro, Protobuf.
- **Schema Registry**:
  - Versioning, evolution rules (backward/forward compatibility)
  - Avoid consumer crashes when schema updates.

---

## 13. Security
- **Transport**: SSL.
- **Authentication**: SASL (PLAIN, SCRAM, OAuth).
- **Authorization**: ACLs (topic, group, cluster).
- Network isolation: private VPC, firewall rules.

---

## 14. Monitoring & Lag Analysis
- Key metrics:
  - Consumer lag
  - Broker disk usage
  - Request handler latency
  - Under-replicated partitions
- Tools: Kafka UI, Grafana, Prometheus, Burrow.
- Lag diagnosis:
  - Slow consumer?
  - Too few partitions?
  - Rebalance storms?

---

## 15. Tuning & Performance Optimization
- Producers:
  - `acks=all`, enable idempotence.
  - Compression for throughput.
- Consumers:
  - Increase partitions for parallelism.
  - Tune fetch size, max.poll settings.
- Brokers:
  - Page cache utilization is critical.
  - Avoid slow disks → SSD recommended.

---

## 16. Operations & Disaster Recovery
- Partition reassignment.
- MirrorMaker 2 for cross-cluster replication.
- Multi-DC:
  - Active-passive vs active-active.
  - Trade-offs: consistency vs availability.
- Backup:
  - Usually rely on replication + external object storage.

---

## 17. KRaft vs ZooKeeper
- **KRaft (Kafka Raft)** replaces ZooKeeper.
- Benefits:
  - Simplified ops
  - Faster metadata propagation
  - Better scalability
- Modern Kafka (3.5+) → ZooKeeper deprecated.

---

# 📚 Essential Resources & System Design Applications

## Interview Quick Reference
**Core concepts (must know):**
- Topics, partitions, offsets, replication factor
- Producer acknowledgments and idempotency
- Consumer groups and rebalancing
- At-least-once vs exactly-once semantics

**System design applications:**
- **Event streaming:** User activity tracking, clickstreams
- **Microservices communication:** Async service-to-service messaging
- **Data integration:** CDC (Change Data Capture), ETL pipelines
- **Real-time analytics:** Stream processing with Kafka Streams

**Common interview questions:**
- "Design a real-time notification system using Kafka"
- "How would you ensure message ordering in Kafka?"
- "Explain how Kafka handles backpressure"
- "Design a data pipeline for processing user events"

## Performance Characteristics
**Throughput benchmarks:**
- Single broker: 100K+ messages/sec
- Cluster: Millions of messages/sec
- Latency: Single-digit milliseconds (properly tuned)

**Scaling considerations:**
- Partitions determine parallelism
- Replication factor affects durability and resource usage
- Consumer group size should match partition count

## Production Best Practices
**Configuration highlights:**
- `acks=all` for durability
- `enable.idempotence=true` for exactly-once semantics
- Proper batch size and linger settings for throughput
- Monitor consumer lag and rebalance frequency

**Books & Resources:**
- [Kafka: The Definitive Guide](pdf/Kafka-The-Definitive-Guide.pdf) - Comprehensive Kafka reference
- [Effective Kafka](pdf/Effective-Kafka.pdf) - Advanced patterns and best practices


