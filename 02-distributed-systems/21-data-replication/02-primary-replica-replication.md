
# 1. Replication Models

Replication is not.

A single architecture.

Different systems.

Replicate data.

In different ways.

Depending on.

Business requirements.

Examples.

- Banking
- Social Media
- E-commerce
- IoT
- Streaming
- Gaming

Each has.

Different consistency.

Availability.

Latency.

And scalability requirements.

---

# The Evolution of Replication

Most systems.

Evolve.

Through these stages.

```text
Single Database

↓

Primary + Replica

↓

Multiple Read Replicas

↓

Multi-Region Replication

↓

Leaderless Global Replication
```

As businesses grow.

Replication architectures.

Become more sophisticated.

---

# Major Replication Models

Most distributed databases.

Use one of.

Three fundamental models.

```text
Replication

│

├── Primary–Replica
│
├── Multi-Leader
│
└── Leaderless
```

Nearly every modern database.

Uses.

One of these.

Or a variation.

---

# Comparison Overview

| Model | Writes | Reads | Complexity | Typical Usage |
|--------|---------|--------|------------|---------------|
| Primary–Replica | One Leader | Replicas | Low | MySQL, PostgreSQL, MongoDB |
| Multi-Leader | Multiple Leaders | Local Replicas | Medium | Multi-region Applications |
| Leaderless | Any Node | Any Node | High | Cassandra, Dynamo, Riak |

---

# Choosing a Replication Model

Architects.

Never begin with.

Technology.

Instead they ask.

```text
Business Requirements

↓

Availability

↓

Consistency

↓

Latency

↓

Replication Model
```

---

# Decision Questions

Before choosing.

A replication model.

Ask yourself.

- Is incorrect data acceptable?
- Can writes happen from multiple regions?
- Is low latency more important than strong consistency?
- How much replication lag is acceptable?
- Can conflicts occur?
- How will conflicts be resolved?
- Is the workload read-heavy or write-heavy?

These answers.

Determine.

The replication strategy.

---

# 2. Primary–Replica Replication (Leader–Follower)

> **One node accepts all writes, and one or more replicas receive copies of those writes.**

This is.

The most common.

Replication model.

Used today.

---

# Architecture

```text
                Write

                  │

                  ▼

          +----------------+
          |    Primary     |
          +----------------+
             │    │    │
             │    │    │
      Replication Replication Replication
             │    │    │
             ▼    ▼    ▼

        Replica Replica Replica
```

---

# Read Flow

```text
           Client

        Read      Write

         │          │

         ▼          ▼

     Replica     Primary
```

Reads.

Can be distributed.

Writes.

Always go.

To the Primary.

---

# Why One Primary?

Imagine.

Three servers.

All accepting writes.

Simultaneously.

Customer A.

Updates.

```text
Address = Pune
```

Customer B.

Updates.

```text
Address = Hyderabad
```

Question.

Which value.

Wins?

Conflict.

Immediately appears.

Having.

One write leader.

Avoids.

This problem.

---

# Responsibilities of the Primary

The Primary.

Typically performs.

- Accept writes
- Validate transactions
- Generate transaction log
- Maintain write ordering
- Replicate changes
- Coordinate failover

Think of the Primary.

As.

The source of truth.

---

# Responsibilities of Replicas

Replicas.

Usually perform.

- Serve read requests
- Maintain copies
- Receive replication updates
- Become Primary after failover
- Improve availability

---

# Example

Suppose.

An e-commerce system.

Receives.

10,000 reads.

Per second.

But only.

500 writes.

Per second.

Architecture.

```text
                Primary

                500 Writes

                 │

      ┌──────────┼──────────┐

      ▼          ▼          ▼

 Replica1   Replica2   Replica3

 3000 RPS   3500 RPS   3500 RPS
```

Read traffic.

Is distributed.

Across replicas.

The Primary.

Handles only writes.

---

# Business Benefits

Primary–Replica.

Provides.

- High read scalability
- Simple architecture
- Easy failover
- Better availability
- Lower operational complexity

---

# Typical Databases

Primary–Replica.

Is used by.

- MySQL
- PostgreSQL
- MongoDB Replica Sets
- SQL Server
- Oracle Data Guard
- MariaDB
- Redis Replication

---

# Read Replicas

A Read Replica.

Is exactly what.

The name suggests.

A database.

Dedicated primarily.

To serving reads.

---

# Example

```text
Users

↓

Application

↓

Load Balancer

↓

Replica A

Replica B

Replica C
```

The application.

Chooses.

A replica.

Instead of.

The Primary.

---

# Read Scaling

Without replicas.

```text
Database

↓

100,000 Reads/sec
```

One database.

Eventually.

Becomes overloaded.

---

With four replicas.

```text
Replica 1

25K Reads

Replica 2

25K Reads

Replica 3

25K Reads

Replica 4

25K Reads
```

Total capacity.

Increases dramatically.

---

# Read Load Balancing

Applications.

May distribute reads.

Using.

- Round Robin
- Least Connections
- Geographic Routing
- Latency-based Routing
- Read Preference
- Consistent Hashing

---

# Geographic Example

```text
India Users

↓

Mumbai Replica

----------------------

Europe Users

↓

Frankfurt Replica

----------------------

US Users

↓

Virginia Replica
```

Nearby replicas.

Reduce latency.

Improve user experience.

---

# Replication Topologies

Replication.

Can be organized.

In several ways.

---

## Star Topology

Most common.

```text
          Primary

        /    |    \

       /     |     \

 Replica Replica Replica
```

Simple.

Easy to manage.

---

## Cascading Replication

One replica.

Replicates.

To another.

```text
Primary

↓

Replica A

↓

Replica B

↓

Replica C
```

Useful.

For reducing.

Primary load.

---

## Tree Topology

```text
            Primary

          /          \

     Replica1      Replica2

      /    \         /    \

    R3     R4      R5     R6
```

Used.

In very large.

Deployments.

---

# Advantages of Primary–Replica

- Easy to understand
- Mature technology
- Excellent read scalability
- Simple conflict handling
- Straightforward backup strategy
- Easy disaster recovery

---

# Limitations

Primary–Replica.

Also introduces.

Several challenges.

---

## Single Write Bottleneck

Every write.

Must reach.

The Primary.

Eventually.

The Primary.

Becomes.

The bottleneck.

---

## Replication Lag

Replicas.

Need time.

To receive.

Updates.

They may return.

Older data.

---

## Failover Complexity

If the Primary.

Fails.

Another replica.

Must become.

The new Primary.

This process.

Requires.

Leader election.

Which we'll study.

In Chapter 23.

---

## Geographic Latency

Suppose.

Primary.

Runs.

In Virginia.

Customer.

Writes.

From India.

Every write.

Travels.

Thousands.

Of kilometers.

Latency increases.

---

# Primary Failure

Suppose.

```text
Primary

↓

💥 Failure
```

Now.

Questions arise.

- Which replica becomes Primary?
- How is split brain avoided?
- What happens to in-flight writes?
- Were any writes lost?

These questions.

Lead directly.

To consensus.

Leader election.

And quorum protocols.

Covered in later chapters.

---

# When to Use Primary–Replica

Choose.

Primary–Replica.

When.

- Reads greatly outnumber writes.
- Strong write ordering is required.
- Operational simplicity is important.
- Applications can tolerate small replication lag.
- One write leader is acceptable.

---

# When NOT to Use

Avoid.

Primary–Replica.

When.

- Every region must accept writes.
- Global write latency must be minimal.
- Write throughput is extremely high.
- Applications require offline writes.
- Multiple active data centers are mandatory.

These situations.

Often require.

Multi-Leader.

Or.

Leaderless replication.

---

# Common Misconceptions

### Misconception 1

> Every replica always contains the latest data.

False.

Replication.

May be asynchronous.

Temporary lag.

Is common.

---

### Misconception 2

> Adding replicas improves write throughput.

False.

Replicas.

Primarily improve.

Read throughput.

Writes.

Still go.

To the Primary.

---

### Misconception 3

> Primary failure automatically means zero downtime.

False.

Failover.

Requires.

Detection.

Leader election.

Promotion.

Client redirection.

---

### Misconception 4

> Read replicas can safely accept writes.

False.

Doing so.

Would create.

Conflicting versions.

Unless.

The system supports.

Multi-Leader replication.

---

# Architect's Perspective

Primary–Replica replication is the default choice for many enterprise applications because it offers an excellent balance between simplicity, scalability, and operational maturity.

However, it has one fundamental limitation:

> **All writes flow through a single leader.**

As systems grow globally, that single write leader can become a bottleneck for latency, throughput, and regional resilience.

This limitation motivates the next replication model:

**Multi-Leader Replication**, where multiple regions can accept writes simultaneously.

---

**End of Part 2**

The next part will cover:

- **Multi-Leader Replication**
- **Leaderless Replication**
- **Comparison of Replication Models**
- **Conflict Detection**
- **Conflict Resolution**
- **Real-world Database Implementations**
