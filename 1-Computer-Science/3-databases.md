# Database Systems

---

# 1. Introduction

## 1.1. Concepts
- DBMS: hệ thống quản lý dữ liệu cơ bản
- RDBMS: dữ liệu quan hệ, schema cố định, SQL, ACID
- NoSQL: phi quan hệ, linh hoạt schema, tối ưu cho phân tán & scale
- ACID: đảm bảo tính toàn vẹn giao dịch
- BASE: hiệu năng cao, eventual consistency

## 1.2. Core Components
- Table, Row, Column
- Schema, View, Constraint
- Index
- Trigger

## 1.3. Database Types
- SQL: PostgreSQL, MySQL, Oracle, SQL Server
- NoSQL: MongoDB (document), Cassandra (columnar), Redis (key-value), Neo4j (graph)

---

# 2. Data Modeling & Database Design

## 2.1. ERD (Entity Relationship Diagram)
- Entity, Attribute
- Relationship: 1–1, 1–N, N–N (qua junction table)

## 2.2. Normalization & Denormalization
- Normalization: loại bỏ dư thừa, tăng toàn vẹn
  - 1NF → atomic
  - 2NF → bỏ partial dependency
  - 3NF → bỏ transitive dependency
  - BCNF → ràng buộc nghiêm hơn
- Denormalization: tối ưu đọc, giảm JOIN
  - Duplicate fields, gộp bảng, materialized views, embedding (NoSQL)

---

# 3. SQL & Query Optimization

## 3.1. SQL Essentials
- DML: SELECT, INSERT, UPDATE, DELETE
- JOIN: INNER, LEFT, RIGHT, FULL
- Aggregation: GROUP BY, HAVING
- Window functions

## 3.2. Subquery & CTE
- Subquery trong WHERE, FROM, SELECT
- CTE: dễ đọc, hỗ trợ recursion

## 3.3. Indexing & Index Strategies
- B-Tree: phổ biến cho range query
- Hash: lookup nhanh theo key
- Composite / Covering index
- Partial index
- Bloom filter index (phân tán)

## 3.4. ORM & Mapping
- N+1 problem
- Lazy vs Eager loading

## 3.5. Query Plan & Optimization
- EXPLAIN/EXPLAIN ANALYZE
- Cost-based optimizer
- Materialized views
- Avoid SELECT *
- Tối ưu JOIN thứ tự, filter sớm

---

# 4. Transactions & Concurrency Control

## 4.1. ACID Properties
- Atomicity, Consistency, Isolation, Durability

## 4.2. Isolation Levels & Problems
- Isolation: RU, RC, RR, Serializable
- Snapshot Isolation
- Lỗi: Dirty Read, Lost Update, Phantom Read, Write Skew

## 4.3. Locking & MVCC
- Locking: pessimistic vs optimistic
- Lock granularity: row/page/table
- MVCC: versioning, tránh lock đọc, GC phiên bản cũ

## 4.4. Recovery & Rollback
- WAL (Write-Ahead Logging)
- Undo/Redo logs
- Checkpoint
- ARIES

---

# 5. Database Architecture & Internals

## 5.1. Pipeline Execution
- Client → Parser → Optimizer → Executor → Storage Engine

## 5.2. Storage Engines
- Row-store vs Column-store
- In-memory DB
- B-Tree vs LSM Tree (Cassandra, RocksDB)

## 5.3. File Format & Buffering
- Page layout (slotted page)
- Buffer pool & eviction (LRU variants)
- Vacuuming, compaction

## 5.4. Logging
- WAL
- Log segments & merging

---

# 6. NoSQL Databases

## 6.1. Types
- Key-Value
- Document
- Column-Family
- Graph

## 6.2. Characteristics
- Schema-less
- BASE model
- Eventual consistency

## 6.3. Internals
- LSM Tree (Cassandra, RocksDB)
- Replication & Sharding
- Query routing
- Secondary index trong NoSQL

---

# 7. Distributed Database Systems

## 7.1. CAP Theorem
- Chỉ đạt tối đa 2 trong 3: C, A, P

## 7.2. Replication & Sharding
- Leader–Follower, Multi-Leader
- Quorum
- Rebalancing shards
- Hotspot handling
- Secondary index trong sharded DB

## 7.3. Consistency Models
- Strong
- Eventual
- Causal
- Tunable (Cassandra)

## 7.4. Consensus & Failover
- Raft, Paxos
- Leader election
- Heartbeat & failure detection

## 7.5. Distributed Transactions
- 2PC, 3PC
- Distributed snapshot

---

# 8. OLTP, OLAP & Modern Data Architecture

## 8.1. OLTP
- CRUD nhanh
- Transactional workloads
- Suitable for real-time operations

## 8.2. OLAP
- Analytical workloads
- Data warehouse
- Batch processing
- ETL vs ELT

## 8.3. Modern Architecture & Data Mesh
- Data-as-a-Product
- Domain ownership
- Self-serve data platform
- Federated governance

## 8.4. Storage & Processing Models
- Column-store
- Compression & encoding
- Delta store
- Vectorized execution

---

# 9. Reference Materials

## Books
- [Readings in Database Systems](./pdf/Redbook-5th-Edition.pdf)
- [Database Internals](pdf/Database-Internals.pdf)
- [Seven Databases in Seven Weeks](pdf/Seven-Databases-in-Seven-Weeks.pdf)
- [NoSQL Distilled](pdf/NoSQL-Distilled.pdf)
- [SQL Performance Explained](pdf/SQL-Performance-Explained.pdf)
