
# 10. Real-World Replication Implementations

Understanding replication theory.

Is important.

Understanding how.

Real production systems.

Implement replication.

Is what makes.

An architect.

This section.

Connects.

Theory.

With production.

Implementations.

---

# Different Databases, Different Choices

Every database.

Makes different trade-offs.

Based on.

Its goals.

```text
Replication

↓

Consistency

↓

Availability

↓

Latency

↓

Business Requirements
```

There is no.

Universal.

Replication architecture.

---

# Database Comparison

| Database | Replication Model | Synchronization | Primary Goal |
|----------|-------------------|-----------------|--------------|
| MySQL | Primary–Replica | Async / Semi-sync | Read Scaling |
| PostgreSQL | Primary–Replica | Async / Sync | Reliability |
| MongoDB | Replica Set | Async + Majority | HA |
| Cassandra | Leaderless | Quorum | Availability |
| DynamoDB | Leaderless (internally coordinated) | Quorum | Global Scale |
| CockroachDB | Multi-Replica + Consensus | Synchronous | Strong Consistency |
| Google Spanner | Multi-Replica + Consensus | Synchronous | Global SQL |
| Kafka | Leader–Follower | ISR Replication | Durable Messaging |
| Redis | Primary–Replica | Async | Fast Reads |

---

# 10.1 MySQL Replication

One of the most.

Widely used.

Replication systems.

---

# Architecture

```text
                Primary

                   │

          Binary Log (Binlog)

                   │

        ┌──────────┼──────────┐

        ▼          ▼          ▼

    Replica1   Replica2   Replica3
```

---

# How It Works

Primary.

Processes.

Every write.

---

The write.

Is recorded.

In.

```text
Binary Log

(Binlog)
```

Replicas.

Read.

The Binlog.

Replay.

The same operations.

Become consistent.

---

# Replication Steps

```text
Client

↓

Primary

↓

Write

↓

Binlog

↓

Replica IO Thread

↓

Replica Relay Log

↓

SQL Thread

↓

Replica Updated
```

---

# Replication Modes

MySQL supports.

- Asynchronous
- Semi-Synchronous
- Group Replication

---

# Advantages

- Mature
- Easy to configure
- Excellent read scaling
- Large ecosystem

---

# Limitations

- Replication lag
- Single write leader
- Failover complexity

---

# Typical Use Cases

- E-commerce
- ERP
- CRM
- Enterprise Applications

---

# 10.2 PostgreSQL Replication

PostgreSQL.

Uses.

Write-Ahead Logging.

(WAL).

---

# Architecture

```text
Client

↓

Primary

↓

WAL

↓

Streaming Replication

↓

Replica
```

---

# What is WAL?

Before modifying.

Database pages.

PostgreSQL writes.

Changes.

To.

```text
Write Ahead Log
```

This log.

Becomes.

The replication source.

---

# Replication Flow

```text
Client

↓

Transaction

↓

WAL

↓

Replica

↓

Replay WAL

↓

Replica Updated
```

---

# Replication Types

PostgreSQL supports.

- Physical Replication
- Logical Replication

---

## Physical Replication

Entire database.

Copied.

Block by block.

---

## Logical Replication

Only.

Selected tables.

Or.

Changes.

Replicated.

Useful.

For migrations.

Microservices.

Selective replication.

---

# Advantages

- Excellent durability
- WAL recovery
- Strong ecosystem
- Flexible replication

---

# Limitations

- Primary write bottleneck
- Cross-region latency

---

# 10.3 MongoDB Replica Sets

MongoDB.

Uses.

Replica Sets.

---

# Architecture

```text
Client

↓

Primary

↓

Secondary A

↓

Secondary B
```

---

# Elections

If Primary.

Fails.

Replica Set.

Performs.

Leader election.

Automatically.

---

# Write Flow

```text
Client

↓

Primary

↓

Secondaries

↓

Majority ACK

↓

Success
```

---

# Read Preferences

Applications.

May choose.

- Primary
- Primary Preferred
- Secondary
- Secondary Preferred
- Nearest

This allows.

Balancing.

Consistency.

Latency.

---

# Write Concern

Applications.

Can choose.

```text
w = 1

Majority

All
```

More acknowledgements.

Increase durability.

Increase latency.

---

# Typical Use Cases

- Content Management
- User Profiles
- Product Catalogs
- JSON Documents

---

# 10.4 Cassandra Replication

Cassandra.

Uses.

Leaderless replication.

---

# Architecture

```text
Client

↓

Coordinator Node

↓

Replica A

↓

Replica B

↓

Replica C
```

---

Notice.

No Primary.

Exists.

---

# Consistency Levels

Applications choose.

Per query.

```text
ONE

QUORUM

LOCAL_QUORUM

ALL

ANY
```

---

# Example

```text
Write

↓

Coordinator

↓

Replica1

↓

Replica2

↓

ACK

↓

Client
```

---

# Repairs

Cassandra.

Uses.

- Read Repair
- Hinted Handoff
- Anti-Entropy Repair

To maintain.

Consistency.

---

# Advantages

- Excellent scalability
- Multi-region support
- High availability
- No single leader

---

# Typical Use Cases

- IoT
- Time Series
- Messaging
- Global Applications

---

# 10.5 Google Spanner

Google Spanner.

Uses.

A completely different.

Approach.

---

# Architecture

```text
Client

↓

Leader Replica

↓

Consensus

↓

Follower Replicas

↓

Commit
```

---

# Key Technologies

Spanner combines.

- Paxos
- TrueTime
- Synchronous Replication

To achieve.

Global consistency.

---

# Write Flow

```text
Client

↓

Leader

↓

Consensus

↓

Commit

↓

Client Success
```

---

# Advantages

- Strong consistency
- Global SQL
- Zero RPO
- Automatic failover

---

# Trade-off

Higher write latency.

Because.

Consensus.

Requires.

Multiple replicas.

---

# Typical Use Cases

- Financial Systems
- Enterprise ERP
- Global Inventory
- Critical Business Systems

---

# 10.6 CockroachDB

CockroachDB.

Builds.

On.

Raft Consensus.

---

# Architecture

```text
Client

↓

Leaseholder

↓

Raft Group

↓

Replicas
```

---

Each data range.

Has.

Its own.

Raft group.

---

Advantages.

- Strong consistency
- Automatic rebalancing
- Multi-region support

---

# 10.7 Kafka Replication

Kafka.

Replicates.

Messages.

Not tables.

---

# Architecture

```text
Producer

↓

Leader Partition

↓

Follower Replicas

↓

Consumers
```

---

# ISR

Kafka.

Maintains.

```text
ISR

In-Sync Replicas
```

Only replicas.

Within ISR.

Can become.

Leader.

---

# Write Flow

```text
Producer

↓

Leader

↓

Followers

↓

Required ACK

↓

Producer Success
```

---

# Acknowledgement Modes

Kafka supports.

```text
acks = 0

acks = 1

acks = all
```

Applications choose.

Latency.

Versus durability.

---

# Typical Use Cases

- Event Streaming
- Messaging
- Log Aggregation
- CDC Pipelines

---

# 10.8 Redis Replication

Redis.

Uses.

Primary–Replica.

Replication.

---

# Architecture

```text
Primary

↓

Replica

↓

Replica
```

---

Replication.

Is generally.

Asynchronous.

---

# Sentinel

Redis Sentinel.

Provides.

- Monitoring
- Failover
- Primary promotion

---

# Redis Cluster

Redis Cluster.

Combines.

- Partitioning
- Replication
- Automatic failover

For.

Horizontal scaling.

---

# Comparison of Real Systems

| System | Replication | Leader | Typical Consistency |
|----------|-------------|---------|---------------------|
| MySQL | Primary–Replica | Yes | Eventual / Semi-sync |
| PostgreSQL | Primary–Replica | Yes | Strong / Eventual |
| MongoDB | Replica Set | Yes | Majority |
| Cassandra | Leaderless | No | Tunable |
| DynamoDB | Leaderless (coordinated) | No single application-visible leader | Tunable |
| CockroachDB | Consensus | Leaseholder | Strong |
| Spanner | Consensus | Leader | Strong |
| Kafka | Leader–Follower | Partition Leader | Strong within partition |
| Redis | Primary–Replica | Yes | Eventual |

---

# Production Architecture Patterns

Large enterprises.

Rarely use.

One database.

For everything.

Example.

```text
Payments

↓

CockroachDB

-----------------------

Catalog

↓

MySQL

-----------------------

Cache

↓

Redis

-----------------------

Events

↓

Kafka

-----------------------

Analytics

↓

Cassandra
```

Each system.

Uses.

Different replication.

Optimized.

For its workload.

---

# Architecture Thinking

One database.

Cannot optimize.

Every requirement.

Different workloads.

Need different.

Replication strategies.

---

# Common Misconceptions

### Misconception 1

> Every database replicates data the same way.

False.

Each database.

Optimizes.

For different goals.

---

### Misconception 2

> Cassandra has no coordination.

False.

Although leaderless.

Requests are coordinated.

Using coordinator nodes.

Quorums.

And repair mechanisms.

---

### Misconception 3

> Kafka replication is the same as database replication.

False.

Kafka replicates.

Ordered log entries.

Not relational data.

---

### Misconception 4

> Majority acknowledgement guarantees zero data loss.

False.

It significantly reduces.

The probability.

Of data loss.

But overall durability.

Also depends on.

Storage.

Consensus.

Failure timing.

And client acknowledgement semantics.

---

# Architect's Decision Framework

Before selecting a replicated data platform, ask:

| Question | Why It Matters |
|----------|----------------|
| Is the workload read-heavy or write-heavy? | Influences replication model |
| Is strong consistency required? | May require consensus-based systems |
| Can the business tolerate replication lag? | Suggests asynchronous replication |
| Are global writes required? | May need multi-leader or globally distributed databases |
| What is the expected scale? | Determines leader-based vs leaderless architecture |
| Is the data relational, document, key-value, or streaming? | Guides technology selection |

---

# Architect's Perspective

Replication is not a feature.

It is one of the **core architectural building blocks** of modern distributed systems.

Every production database makes different replication trade-offs to optimize for:

- Availability
- Consistency
- Latency
- Throughput
- Operational simplicity
- Cost

Great architects do not ask:

> **"Which database has replication?"**

They ask:

> **"Which replication model best matches this workload's business requirements?"**

That question leads to better architecture decisions than comparing technologies alone.

---

# Chapter 21 Summary

## Core Concepts

- ✅ Why Replication Exists
- ✅ Replication vs Backup
- ✅ Business Goals of Replication

## Replication Models

- ✅ Primary–Replica
- ✅ Multi-Leader
- ✅ Leaderless

## Synchronization

- ✅ Synchronous
- ✅ Asynchronous
- ✅ Semi-Synchronous

## Read Consistency

- ✅ Replication Lag
- ✅ Eventual Consistency
- ✅ Read-after-Write
- ✅ Monotonic Reads
- ✅ Consistent Prefix
- ✅ Session Consistency

## Advanced Replication

- ✅ Quorum Replication (N, R, W)
- ✅ Read Repair
- ✅ Hinted Handoff
- ✅ Anti-Entropy

## Conflict Handling

- ✅ Conflict Detection
- ✅ Conflict Resolution
- ✅ Last Write Wins
- ✅ Merge
- ✅ Vector Clocks
- ✅ CRDTs
- ✅ Operational Transformation

## High Availability

- ✅ Failover
- ✅ Failback
- ✅ Split Brain
- ✅ Disaster Recovery
- ✅ RPO & RTO

## Real-World Systems

- ✅ MySQL
- ✅ PostgreSQL
- ✅ MongoDB
- ✅ Cassandra
- ✅ CockroachDB
- ✅ Google Spanner
- ✅ Kafka
- ✅ Redis

---

**End of Chapter 21 – Data Replication**

