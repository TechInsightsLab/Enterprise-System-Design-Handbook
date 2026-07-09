
# 114. Interview Preparation

This section contains questions commonly asked in interviews ranging from Software Engineer to Principal Architect.

The goal is not only to memorize answers but to understand the reasoning behind architectural decisions.

---

# Beginner Level

## Fundamental Questions

### 1. What is Data Management?

**Expected Answer**

Data Management is the practice of collecting, storing, organizing, securing, governing, processing, and maintaining data throughout its lifecycle while ensuring correctness, availability, security, and business value.

---

### 2. What is the difference between Data and Information?

**Answer**

| Data | Information |
|------|-------------|
| Raw facts | Processed meaningful data |
| Example: `50000` | Monthly Sales = ₹50,000 |
| No context | Business context available |

---

### 3. What is Metadata?

Expected Answer:

Metadata is data about data.

Example:

```
Customer Table

↓

Column Name

↓

VARCHAR(100)

↓

Owner

↓

Retention Policy
```

---

### 4. What is Master Data?

Examples:

- Customer
- Product
- Employee
- Supplier

Master data changes slowly and is shared across multiple systems.

---

### 5. What is Transactional Data?

Examples:

- Orders
- Payments
- Shipments
- Refunds

Transactional data continuously grows as business operations occur.

---

### 6. Explain Normalization.

Expected points:

- Remove duplication
- Improve consistency
- Reduce anomalies
- Improve integrity

---

### 7. What are the Normal Forms?

- 1NF
- 2NF
- 3NF
- BCNF

Know the purpose of each.

---

### 8. Difference between Normalization and Denormalization?

| Normalization | Denormalization |
|---------------|-----------------|
| Reduce redundancy | Improve read performance |
| More joins | Fewer joins |
| Better updates | Faster reads |

---

### 9. What is a Primary Key?

Uniquely identifies a row.

Should be:

- Unique
- Stable
- Non-null

---

### 10. What is a Foreign Key?

Represents a relationship between tables.

Maintains referential integrity.

---

# Intermediate Level

## Database Design

### 11. Difference between SQL and NoSQL?

Expected discussion:

- Schema
- Transactions
- Scalability
- Consistency
- Use cases

---

### 12. Explain Polyglot Persistence.

Using multiple database technologies within the same application because different workloads have different requirements.

---

### 13. When would you choose MongoDB over PostgreSQL?

Expected Answer

MongoDB:

- Flexible schema
- Nested documents
- Rapid evolution

PostgreSQL:

- Strong consistency
- Complex joins
- Financial systems

---

### 14. Explain Sharding.

Expected discussion:

- Horizontal scaling
- Shard key
- Routing
- Hot shards
- Rebalancing

---

### 15. Difference between Partitioning and Sharding?

Interview favorite.

---

### 16. Explain Replication.

Topics:

- Primary
- Replica
- Synchronous
- Asynchronous
- Read Replica

---

### 17. What is CAP Theorem?

Explain:

Consistency

Availability

Partition Tolerance

Discuss trade-offs during network partitions.

---

### 18. Explain ACID.

Must know:

Atomicity

Consistency

Isolation

Durability

---

### 19. Difference between ACID and BASE?

Strong consistency

vs

Eventual consistency.

---

### 20. Explain Point-in-Time Recovery.

Restore database to a precise moment using:

- Backup
- WAL
- Transaction Logs

---

# Senior Engineer Level

### 21. Design a Database for an E-Commerce Platform.

Discuss:

- Customers
- Orders
- Inventory
- Payments
- Products

Explain relationships.

Normalization.

Indexes.

---

### 22. Design a Multi-Tenant Database.

Expected discussion:

- Shared schema
- Separate schema
- Separate database
- Tenant isolation
- Security
- Scaling

---

### 23. Design a Product Catalog.

Topics:

- Flexible attributes
- Categories
- Search
- Inventory
- Images

Database choice matters.

---

### 24. How would you archive historical data?

Discuss:

- Lifecycle
- Cold storage
- Partitioning
- Compliance

---

### 25. Design Backup Strategy.

Should include:

- Full
- Incremental
- PITR
- DR
- Testing

---

### 26. Explain CDC.

Discuss:

- WAL
- Debezium
- Kafka
- Event-driven architecture

---

### 27. Explain Event Sourcing.

Discuss:

- Immutable events
- Replay
- Snapshot
- Audit

---

### 28. How would you migrate a large database?

Topics:

- Backward compatibility
- Online migration
- Dual write (carefully)
- CDC
- Validation
- Cutover strategy

---

### 29. Explain Schema Evolution.

Cover:

- Backward compatibility
- Rolling deployment
- Versioning

---

### 30. Explain Data Governance.

Expected topics:

- Ownership
- Classification
- Security
- Compliance
- Stewardship

---

# Solution Architect Level

### 31. Design a Global Database.

Discuss:

- Multi-region
- Replication
- Geo-partitioning
- Data locality
- Latency
- Failover

---

### 32. How would you reduce database latency?

Possible solutions:

- Caching
- Read replicas
- Indexing
- Query optimization
- Partitioning
- Data locality

---

### 33. Design Data Storage for Netflix.

Expected architecture:

- Metadata
- Videos
- Search
- Recommendations
- Analytics

Different storage technologies.

---

### 34. Design Banking Database.

Should discuss:

- ACID
- Transactions
- Isolation
- Auditing
- Encryption
- PITR

---

### 35. Design a Billion User Social Network.

Expected:

- Partitioning
- Graph database
- Search
- Cache
- Feed generation

---

### 36. How would you eliminate database bottlenecks?

Topics:

- Vertical scaling
- Horizontal scaling
- Read replicas
- Caching
- Query tuning
- Sharding

---

### 37. How do you choose a shard key?

Must discuss:

- Uniform distribution
- Growth
- Query patterns
- Hotspot prevention

---

### 38. Explain eventual consistency to business stakeholders.

Architects should explain:

Business impact.

Trade-offs.

User experience.

Not only technical theory.

---

### 39. Explain database selection process.

Interviewers expect structured thinking rather than naming favorite technologies.

---

### 40. What database would you choose for these scenarios?

| Scenario | Database |
|-----------|----------|
| Banking | PostgreSQL / Aurora / Spanner |
| Product Catalog | MongoDB |
| Cache | Redis |
| Analytics | Redshift / BigQuery |
| Graph | Neo4j |
| Monitoring | Timestream / InfluxDB |

Explain **why**, not just **what**.

---

# Principal Architect Level

### 41. Design a Global Payment Platform.

Discuss:

- Multi-region writes
- Strong consistency
- Disaster Recovery
- Compliance
- Encryption
- Audit

---

### 42. How would you migrate a 100 TB database with zero downtime?

Expected discussion:

- CDC
- Dual-read / validation
- Incremental migration
- Canary rollout
- Cutover
- Rollback plan

---

### 43. Explain how Google Spanner achieves global consistency.

High-level discussion:

- Distributed consensus
- Time synchronization concepts
- Global replication

No need for implementation internals unless specifically asked.

---

### 44. How would you design data architecture for AI systems?

Topics:

- Feature store
- Data lake
- Vector database
- Metadata
- Governance
- Training datasets

---

### 45. Design enterprise data governance for a multinational organization.

Discuss:

- Ownership
- Compliance
- Classification
- Data lineage
- Stewardship
- Catalog
- Audit

---

# Common Follow-up Questions

Interviewers often ask:

- Why not NoSQL?
- Why not SQL?
- Why not Event Sourcing?
- Why not CQRS?
- Why not one database?
- Why cache?
- Why replication?
- Why not replication?
- Why not sharding?
- How would you migrate?
- What would you monitor?

Always relate answers back to:

- Business requirements
- Trade-offs
- Cost
- Operational complexity

---

# 115. One-Page Revision Cheat Sheet

## Core Concepts

- Data is a business asset.
- Model the business before designing tables.
- Separate conceptual, logical, and physical models.
- Normalize for correctness; denormalize for performance when justified.
- Choose databases based on workload, not popularity.
- Partition before sharding when possible.
- Replication improves availability and read scalability.
- ACID ensures transactional correctness.
- BASE enables scalable distributed systems.
- CAP describes trade-offs during network partitions.
- PACELC explains latency vs consistency trade-offs even without failures.
- Encrypt data at rest and in transit.
- Rotate keys and externalize secrets.
- Backups are useless unless restores are tested.
- Data governance is as important as database technology.

---

# 116. Architect's Golden Principles

1. Treat data as the organization's most valuable asset.
2. Model the business before modeling the database.
3. Keep one authoritative source of truth for each business entity.
4. Choose storage technology based on access patterns.
5. Optimize for correctness before optimization.
6. Prefer simple architectures until scale demands otherwise.
7. Design for failure, backup, and recovery from day one.
8. Build security into every layer of the data lifecycle.
9. Continuously validate backups and disaster recovery.
10. Every data architecture decision should support business objectives.

---

# 117. Chapter Completion Checklist

```markdown
- [x] Data Management Fundamentals
- [x] Data Lifecycle
- [x] Data Ownership
- [x] Metadata
- [x] Data Modeling
- [x] Normalization & Denormalization
- [x] DDD Aggregates
- [x] SQL & NoSQL
- [x] Database Types
- [x] OLTP vs OLAP
- [x] Data Warehouse
- [x] Data Lake & Lakehouse
- [x] ETL & ELT
- [x] Partitioning
- [x] Sharding
- [x] Replication
- [x] ACID & BASE
- [x] CAP & PACELC
- [x] Consistency Models
- [x] WAL & CDC
- [x] Event Sourcing
- [x] Schema Evolution
- [x] Backup & Disaster Recovery
- [x] Data Security
- [x] Governance & Compliance
- [x] Cloud Database Services
- [x] Enterprise Case Studies
- [x] Production Incidents
- [x] ADR
- [x] Interview Questions
- [x] Revision Cheat Sheet
```

---

# 118. Chapter Summary

Data Management is the foundation of every enterprise system.

While applications, APIs, and infrastructure evolve continuously, **data persists for years—often decades—and becomes increasingly valuable over time**.

A successful architect must understand far more than database syntax. They must design:

- Business-centric data models
- Appropriate storage technologies
- Scalable partitioning and sharding strategies
- Reliable replication and recovery mechanisms
- Secure and compliant data platforms
- Governed and observable data ecosystems

Modern enterprises rarely rely on a single database. Instead, they embrace **polyglot persistence**, selecting the right technology for each workload while carefully managing consistency, operational complexity, and business risk.

Ultimately, good data architecture is measured not only by performance, but by its ability to preserve **business correctness, trust, and continuity** under growth, failure, and change.

---

# Connection to Previous Chapters

| Chapter | Primary Question |
|----------|------------------|
| High Availability | Can users reach the system? |
| Reliability | Can users trust the system? |
| Scalability | Can the system grow? |
| Performance | Can it respond quickly? |
| Fault Tolerance | Can it continue despite failures? |
| Resilience | Can it recover and adapt? |
| Consistency | Is the data logically correct? |
| Distributed Transactions | Can multiple services complete one business operation? |
| Concurrency | Can many users safely modify shared data? |
| **Data Management** | **How is business data modeled, stored, secured, governed, and evolved over time?** |

Data Management naturally leads to the next architectural concern:

> **Chapter 11 – Caching**, where we explore how to reduce latency, improve scalability, minimize database load, and design high-performance distributed caching architectures.

---

## Suggested Enhancements for Future Editions

### Advanced Topics

- Data Lineage
- Data Catalogs
- Master Data Management (MDM)
- Data Mesh
- Data Fabric
- Vector Databases
- Feature Stores for Machine Learning
- CRDT-based Replication
- Multi-Leader Replication
- Zero-ETL Architectures
- Database Internals (B+ Trees, LSM Trees, MVCC internals)

### Hands-on Labs

- Design an e-commerce schema from requirements
- Normalize a legacy database to 3NF
- Implement optimistic locking in JPA
- Configure PostgreSQL streaming replication
- Build CDC with Debezium and Kafka
- Perform a zero-downtime schema migration
- Restore a database using PITR
- Design a multi-region database architecture

### Architecture Exercises

- Design a banking data platform
- Design a global SaaS multi-tenant database
- Build a real-time analytics platform
- Review a production schema for scalability issues
- Choose storage technologies for a modern e-commerce platform

---

> **Chapter 10 – Data Management Complete**

