
# 30. Consistency in Real Systems

> **Understanding consistency models is important. Understanding how real production systems implement them is what distinguishes an architect from an engineer.**

Every database, messaging system, cache, and distributed platform chooses a different balance between:

- Consistency
- Availability
- Latency
- Scalability
- Operational Complexity

There is **no universal implementation**.

Each technology optimizes for different business requirements.

---

# Technology Landscape

```text
                   Consistency

                         ▲

      Relational DBs     │      NewSQL

                         │

                         │

      Redis              │

                         │

      Kafka              │

                         │

      Cassandra          │

                         └────────────────────►

               Availability & Scalability
```

---

# 30.1 Relational Databases

Traditional relational databases prioritize correctness.

Examples:

- PostgreSQL
- MySQL
- Oracle
- SQL Server

---

# Architecture

```text
Application

↓

Primary Database

↓

Replicas
```

Writes normally occur on:

```
Primary
```

Reads:

- Primary
- Replicas

depending on configuration.

---

# Consistency

Primary reads:

```
Strong
```

Replica reads:

```
May be stale
```

---

# Example

Customer updates profile.

Immediately reads from:

Primary.

↓

Latest data.

Reads from replica:

↓

Possibly stale.

---

# Strengths

- ACID transactions
- Strong consistency
- Mature ecosystem
- Excellent integrity

---

# Limitations

- Write scalability
- Multi-region complexity
- Replica lag

---

# Typical Use Cases

- Banking
- ERP
- Inventory
- Financial systems

---

# 30.2 NoSQL Databases

NoSQL databases typically prioritize:

- Scalability
- Availability
- Flexible schemas

Many support configurable consistency.

---

# Cassandra

Architecture:

```text
Client

↓

Replica A

Replica B

Replica C
```

Leaderless.

Uses:

- Quorum
- Eventual consistency
- Read repair
- Hinted handoff

---

# Tunable Consistency

Example:

```
ONE

QUORUM

ALL
```

Architects choose per query.

---

# MongoDB

Uses:

Primary

↓

Secondary Replicas

Consistency depends on:

- Read preference
- Write concern

Example:

```text
Read from Primary

↓

Strong

Read from Secondary

↓

Eventual
```

---

# DynamoDB

Supports:

Eventually Consistent Reads

and

Strongly Consistent Reads

Example

Shopping Cart:

```
Strong Read
```

Recommendation Engine:

```
Eventual Read
```

One database supports multiple consistency models.

---

# Couchbase

Provides:

- Configurable durability
- Replication
- Distributed caching

Designed for:

Low latency.

---

# Comparison

| Database | Default Model |
|-----------|---------------|
| Cassandra | Eventual |
| DynamoDB | Eventual (Strong Optional) |
| MongoDB | Primary Strong |
| Couchbase | Eventual |

---

# 30.3 Kafka Consistency

Kafka provides strong ordering guarantees within a partition.

---

# Architecture

```text
Producer

↓

Leader Partition

↓

Follower

↓

Follower

↓

Consumer
```

---

# Producer Acknowledgements

```
acks = 0

acks = 1

acks = all
```

Each provides different guarantees.

---

# Idempotent Producer

Kafka prevents duplicate writes.

Useful during:

Retries.

---

# Transactions

Kafka supports:

Exactly-once semantics

using:

- Transactions
- Producer IDs
- Consumer offsets

---

# Ordering

Guaranteed:

Within one partition.

Not guaranteed:

Across partitions.

---

# Example

Partition A

```
Order1

↓

Order2

↓

Order3
```

Ordering preserved.

---

# 30.4 Redis Consistency

Redis prioritizes:

Speed.

---

# Primary Replica

```text
Primary

↓

Replica
```

Replication:

Asynchronous.

---

# Result

Reads from replica may observe:

Old values.

---

# Redis Sentinel

Provides:

- Monitoring
- Automatic failover
- Leader election

---

# Redis Cluster

Provides:

- Sharding
- Replication
- High availability

Consistency depends on:

Replication timing.

---

# Typical Use Cases

- Caching
- Sessions
- Rate limiting
- Leaderboards

Not suitable as the sole source of truth for financial records.

---

# 30.5 Spring Boot Consistency

Application architecture also influences consistency.

---

# Transaction Management

Spring provides:

```java
@Transactional
```

Guarantees:

- Atomicity
- Isolation
- Consistency (database level)

---

# Distributed Transactions

Avoid:

```text
Service A

↓

Service B

↓

Service C

↓

Global Transaction
```

Instead,

prefer:

- Saga Pattern
- Event-driven architecture
- Idempotency

---

# Retry Considerations

Retries without idempotency create:

- Duplicate payments
- Duplicate orders
- Duplicate notifications

---

# Example

Good practice:

```text
Retry

+

Idempotency Key
```

---

# Read Your Own Writes

Example:

Customer updates profile.

Immediately:

Read from:

Primary.

Avoid stale replicas.

---

# 30.6 Kubernetes Consistency

Kubernetes itself depends heavily on distributed consistency.

---

# etcd

Kubernetes stores cluster state in:

```
etcd
```

---

# Why etcd?

Uses:

Raft Consensus.

Guarantees:

Strong consistency.

---

# Cluster State

Every Kubernetes component agrees on:

- Pods
- Nodes
- Services
- Secrets
- ConfigMaps

---

# API Server

Writes:

```
API Server

↓

etcd

↓

Commit
```

Cluster remains consistent.

---

# Leader Election

Controllers use:

Leader election

to avoid:

Split-brain.

---

# 30.7 Cloud-Native Consistency

Cloud providers offer different consistency guarantees.

Architects should understand these differences.

---

# AWS

| Service | Consistency |
|-----------|-------------|
| DynamoDB | Eventual / Strong |
| S3 | Strong Read-after-Write (modern implementation) |
| Aurora | Strong (Primary) |
| ElastiCache Redis | Replica Eventually Consistent |
| EFS | Strong Metadata Consistency |

---

# Azure

| Service | Consistency |
|-----------|-------------|
| Cosmos DB | Five Consistency Levels |
| Azure SQL | Strong |
| Azure Cache for Redis | Eventual Replication |
| Azure Blob Storage | Strong Read-after-Write |

---

# Google Cloud

| Service | Consistency |
|-----------|-------------|
| Spanner | External Strong Consistency |
| Bigtable | Strong Row Consistency |
| Firestore | Strong Document Consistency |
| Memorystore | Eventual Replication |

---

# Azure Cosmos DB

One of the most flexible consistency implementations.

Supports:

- Strong
- Bounded Staleness
- Session
- Consistent Prefix
- Eventual

Architect chooses.

---

# 31. Strong vs Eventual Consistency

A practical comparison.

---

# Banking

Requirement:

```
Correct Balance
```

Choice:

Strong consistency.

---

# Social Media Feed

Requirement:

```
Fast Timeline
```

Choice:

Eventual consistency.

---

# Shopping Cart

Requirement:

Customer sees own updates.

Choice:

Read-after-write.

---

# Chat Messages

Requirement:

Messages appear in order.

Choice:

Consistent Prefix.

---

# Collaborative Editing

Requirement:

Cause before effect.

Choice:

Causal consistency.

---

# Product Catalog

Requirement:

Millions of reads.

Minor delay acceptable.

Choice:

Eventual consistency.

---

# Comparison Matrix

| Business Requirement | Recommended Model |
|----------------------|-------------------|
| Bank Balance | Strong |
| Payment Processing | Strong |
| Inventory Reservation | Strong |
| Shopping Cart | Session |
| User Preferences | Read-after-Write |
| Product Catalog | Eventual |
| Analytics Dashboard | Weak |
| Chat | Consistent Prefix |
| Collaborative Editing | Causal |
| Social Media | Eventual |

---

# 32. Implementation Decision Matrix

| Technology | Default Consistency | Best Use Cases |
|------------|---------------------|----------------|
| PostgreSQL | Strong | Banking, ERP |
| MySQL | Strong | Business Applications |
| Cassandra | Eventual | Large-scale Write Systems |
| DynamoDB | Tunable | Cloud Applications |
| MongoDB | Primary Strong | General Applications |
| Kafka | Ordered per Partition | Event Streaming |
| Redis | Eventual Replica | Caching |
| CockroachDB | Strong | Global SQL |
| Google Spanner | External Strong | Financial Systems |
| Cosmos DB | Configurable | Multi-model Cloud Apps |

---

# Cloud Mapping

| Requirement | AWS | Azure | GCP |
|--------------|-----|--------|-----|
| Strong SQL | Aurora | Azure SQL | Cloud SQL |
| Global Strong Consistency | — | Cosmos DB (Strong Mode)* | Spanner |
| Distributed NoSQL | DynamoDB | Cosmos DB | Bigtable |
| Distributed Cache | ElastiCache | Azure Cache for Redis | Memorystore |
| Event Streaming | MSK | Event Hubs | Pub/Sub |

> *Cosmos DB offers configurable consistency levels; its behavior depends on the selected consistency mode.

---

# Cost Analysis

| Choice | Infrastructure Cost | Latency | Scalability | Correctness |
|----------|---------------------|----------|--------------|-------------|
| Strong Consistency | High | High | Medium | Excellent |
| Eventual Consistency | Low | Low | Excellent | Moderate |
| Quorum | Medium | Medium | High | High |
| Multi-Leader | High | Low Regional | High | Conflict Handling Required |

---

# Common Myths

### Myth

NoSQL databases are always eventually consistent.

**Reality**

Many NoSQL databases provide configurable or even strong consistency modes.

---

### Myth

SQL databases are always strongly consistent.

**Reality**

Reads from asynchronous replicas can return stale data.

---

### Myth

Kafka guarantees ordering everywhere.

**Reality**

Kafka guarantees ordering **within a partition**, not across all partitions.

---

### Myth

Redis replication is synchronous.

**Reality**

Redis replication is typically asynchronous, so replicas may temporarily lag.

---

# Staff / Principal Architect Corner Cases

Consider these design scenarios:

- Can a globally distributed shopping cart use strong consistency everywhere?
- How would you avoid duplicate payments during retries?
- What consistency model would you choose for a multiplayer online game?
- How would you migrate from eventual consistency to strong consistency?
- Can you combine different consistency models within the same application?

These are common topics in senior architecture interviews because they require balancing business requirements with distributed systems trade-offs.

---

# Architect's Perspective

The best architects rarely ask:

> **"Which database has the strongest consistency?"**

Instead, they ask:

- Which business workflows require strict correctness?
- Which workflows tolerate temporary inconsistency?
- Which consistency guarantee minimizes customer impact?
- Which choice meets latency and scalability goals?

In modern systems, **one application often uses multiple consistency models**:

- Strong consistency for payments.
- Session consistency for shopping carts.
- Eventual consistency for product catalogs.
- Consistent prefix for messaging.
- Weak consistency for analytics.

The architecture succeeds not because every component uses the strongest guarantee, but because **each component uses the most appropriate guarantee for its business responsibility**.

---

**End of Part 6**

The next section will cover:

- **Consistency Monitoring**
- **Observability**
- **Production Incidents**
- **Real Enterprise War Stories**
- **Consistency Anti-patterns**
- **Architecture Review Checklist**
- **Production Readiness Checklist**
- **Architecture Decision Record (ADR)**
- **Decision Tree**
