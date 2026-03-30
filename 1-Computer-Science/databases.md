# Database Systems for Backend Engineers

> Essential database knowledge for scalable backend systems and system design interviews

## 🎯 Why Database Knowledge Matters
- **System design interviews:** Choose right database type, design data models, handle scaling
- **Performance optimization:** Query tuning, indexing strategies, connection management
- **Architecture decisions:** ACID vs BASE, consistency models, sharding strategies
- **Troubleshooting:** Debug slow queries, connection issues, transaction problems
- **Modern applications:** Handle high traffic, distributed systems, microservices data patterns

---

# 1. Database Fundamentals & Trade-offs

## SQL vs NoSQL Quick Decision Guide
• **Consistency**: SQL → ACID (Strong), NoSQL → BASE (Eventual)
• **Schema**: SQL → Fixed/structured, NoSQL → Flexible/evolving
• **Scaling**: SQL → Vertical, NoSQL → Horizontal
• **Queries**: SQL → Complex joins, NoSQL → Simple/specific patterns
• **Use Cases**: SQL → Financial/traditional, NoSQL → Social/real-time/big data

## NoSQL Types Cheat Sheet
- **📄 Document:** MongoDB (flexible JSON-like)
- **🔑 Key-Value:** Redis, DynamoDB (fast, simple)
- **📊 Column:** Cassandra (wide rows, analytics)
- **🕸️ Graph:** Neo4j (relationships, social networks)

## When to Choose What?
**✅ SQL:** Transactions, complex relationships, strict consistency
**✅ NoSQL:** Scale, flexibility, specific access patterns

**Interview insight:** There's no "best" database - only trade-offs based on requirements

---

# 2. 📐 Data Modeling Quick Guide

## Normalization vs Denormalization
• **Normalized**: No duplicates, consistency → More JOINs, complex → Transactional systems
• **Denormalized**: Fast reads, simple queries → Data duplication → Read-heavy, analytics

## NoSQL Modeling Patterns
- **📄 Document:** Embed related data → User profiles, product catalogs
- **🔑 Key-Value:** Simple lookups → Sessions, caching
- **📊 Column-family:** Wide rows → Time-series, logs
- **🕸️ Graph:** Relationships → Social networks, recommendations

**💡 Key Principle:** NoSQL = Design for access patterns, not relationships

---

# 3. Query Optimization & Performance

## 3.1 SQL Query Optimization
**Index strategies:**
- **B-Tree indexes:** Range queries, sorting (most common)
- **Hash indexes:** Exact matches only
- **Composite indexes:** Multi-column queries
- **Covering indexes:** Include all needed columns

**Query optimization techniques:**
- **Filter early:** Push WHERE clauses down
- **Avoid SELECT *:** Only select needed columns
- **JOIN optimization:** Understand execution order
- **Use EXPLAIN:** Analyze query execution plans

## 3.2 Common Performance Issues
**N+1 Query Problem:**
- Problem: One query to get list, N queries to get details
- Solution: Use JOINs, batch loading, or eager loading

**Missing indexes:**
- Symptom: Full table scans in EXPLAIN
- Solution: Add indexes on WHERE, ORDER BY, JOIN columns

**Large result sets:**
- Problem: Loading too much data into memory
- Solution: Pagination, streaming, cursor-based pagination

**Interview insight:** Always discuss indexing strategy and query patterns in data design questions

---

# 4. Transactions & Consistency

## 4.1 ACID Properties Deep-dive
**Atomicity:** All-or-nothing transaction execution
**Consistency:** Database integrity constraints maintained
**Isolation:** Concurrent transactions don't interfere
**Durability:** Committed changes survive system failures

## 4.2 Isolation Levels & Real-world Trade-offs
**Read Uncommitted:** Dirty reads possible (rarely used)
**Read Committed:** Most common default, prevents dirty reads
**Repeatable Read:** Prevents non-repeatable reads
**Serializable:** Strongest isolation, can impact performance

**Concurrency issues:**
- **Dirty Read:** Reading uncommitted changes
- **Non-repeatable Read:** Same query returns different results
- **Phantom Read:** New rows appear in range queries
- **Lost Update:** Concurrent updates overwrite each other

## 4.3 Locking vs MVCC
**Locking approach:** Pessimistic concurrency control
- Pros: Prevents conflicts upfront
- Cons: Can cause deadlocks, reduced concurrency

**MVCC (Multi-Version Concurrency Control):**
- Each transaction sees consistent snapshot
- Readers don't block writers
- Used by PostgreSQL, Oracle, MySQL InnoDB

**Interview tip:** Understand your database's concurrency model and its implications for application design

---

# 5. Database Scaling & Distributed Systems

## 5.1 Scaling Strategies
**Vertical Scaling (Scale-up):**
- Bigger servers, more CPU/RAM
- Pros: Simple, no application changes
- Cons: Hardware limits, single point of failure

**Horizontal Scaling (Scale-out):**
- **Read Replicas:** Distribute read traffic across multiple servers
- **Sharding:** Split data across multiple databases
- **Federation:** Split databases by function (users, orders, products)

## 5.2 Sharding Patterns
**Shard key selection:**
- **Range-based:** Partition by value range (A-M, N-Z)
- **Hash-based:** Distribute using hash function
- **Directory-based:** Lookup service to find correct shard

**Challenges:**
- **Hot spots:** Uneven data/traffic distribution
- **Cross-shard queries:** Complex joins across shards
- **Rebalancing:** Moving data when adding/removing shards

## 5.3 CAP Theorem & Consistency Models
**CAP Theorem:** Can only guarantee 2 of 3:
- **Consistency:** All nodes see the same data simultaneously
- **Availability:** System remains operational
- **Partition tolerance:** System continues despite network failures

**Real-world applications:**
- **CP systems:** Traditional RDBMS (choose consistency over availability)
- **AP systems:** NoSQL databases (choose availability over consistency)
- **CA systems:** Not practical in distributed environments

**Consistency models:**
- **Strong consistency:** All reads see the latest write
- **Eventual consistency:** System becomes consistent over time
- **Causal consistency:** Related operations appear in order

**Interview insight:** Understand trade-offs, not just definitions

---

# 6. Modern Database Architecture & Patterns

## 6.1 OLTP vs OLAP Workloads
**OLTP (Online Transaction Processing):**
- **Characteristics:** High volume, short transactions, real-time
- **Examples:** E-commerce, banking, user management
- **Database choice:** Row-oriented, ACID compliant

**OLAP (Online Analytical Processing):**
- **Characteristics:** Complex queries, historical data, batch processing
- **Examples:** Business intelligence, data warehouses, reporting
- **Database choice:** Column-oriented, optimized for aggregations

**Modern pattern:** Separate OLTP and OLAP systems, use ETL/ELT to sync data

## 6.2 Connection Management & Performance
**Connection pooling:**
- Reuse database connections instead of creating new ones
- Configure pool size based on database limits and application needs
- Monitor connection pool metrics

**Query performance:**
- **Slow query logs:** Identify bottleneck queries
- **Connection limits:** Database can only handle so many concurrent connections
- **Read/write separation:** Route queries to appropriate replicas

## 6.3 Caching Strategies
**Database-level caching:**
- **Query result cache:** Cache frequently executed queries
- **Buffer pool:** Cache frequently accessed pages in memory

**Application-level caching:**
- **Cache-aside:** Application manages cache explicitly
- **Write-through:** Write to cache and database simultaneously
- **Write-behind:** Write to cache first, database later

**Distributed caching:** Redis, Memcached for shared cache across application instances

---

# 7. Database Patterns for System Design

## 7.1 Common System Design Database Patterns
**User data storage:**
- Profile data: SQL database for consistency
- Session data: Redis for fast access
- Activity logs: NoSQL for scale

**E-commerce platform:**
- Product catalog: Document database for flexibility
- Inventory: SQL for ACID transactions
- Order history: Sharded SQL or NoSQL

**Social media feed:**
- User posts: Document database
- Relationships: Graph database or SQL
- Feed generation: Cached materialized views

## 7.2 Microservices Data Patterns
**Database per microservice:**
- Each service owns its data
- Avoid shared databases between services
- Use API calls for cross-service data access

**Event sourcing:**
- Store events instead of current state
- Rebuild state by replaying events
- Good for audit trails and complex business logic

**CQRS (Command Query Responsibility Segregation):**
- Separate read and write models
- Optimize read queries independently
- Often combined with event sourcing

**Saga pattern:**
- Manage distributed transactions across services
- Choreography vs Orchestration approaches
- Handle compensating actions for failures

## 7.3 Real-world Considerations
**Data migration strategies:**
- Blue-green deployments for schema changes
- Gradual migration using dual writes
- Backward compatibility considerations

**Monitoring and observability:**
- Query performance monitoring
- Connection pool metrics
- Replication lag monitoring
- Database resource utilization

**Security patterns:**
- Principle of least privilege
- Encryption at rest and in transit
- SQL injection prevention
- Access auditing

---

# 📚 Essential Resources & Quick Reference

## Must-Read Books
- [Database Internals](pdf/Database-Internals.pdf) - How databases work under the hood
- [Readings in Database Systems](pdf/Redbook-5th-Edition.pdf) - Classic papers and research
- [Seven Databases in Seven Weeks](pdf/Seven-Databases-in-Seven-Weeks.pdf) - Practical NoSQL overview

## Interview Quick Reference
**System design applications:**
- Choose database type based on requirements (consistency, scale, complexity)
- Design sharding strategy for horizontal scaling
- Explain caching layers and their trade-offs
- Discuss OLTP vs OLAP separation

**Technical deep-dive topics:**
- SQL query optimization and indexing strategies
- Transaction isolation levels and their implications
- CAP theorem trade-offs in distributed systems
- Connection pooling and performance optimization

**Common interview questions:**
- "Design the database for a social media platform"
- "How would you scale a database to handle 10x traffic?"
- "Explain ACID properties and when you might relax them"
- "How do you handle eventual consistency in microservices?"

