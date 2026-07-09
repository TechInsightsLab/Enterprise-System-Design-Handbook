
# 39. Kafka Deep Dive

> **Apache Kafka is a distributed, fault-tolerant, append-only event streaming platform designed to handle millions of events per second while preserving durability and scalability.**

Previous section introduced Kafka.

This section explores:

How Kafka actually works internally.

Understanding Kafka internals is one of the most important skills for a Solution Architect.

---

# Why Study Kafka Internals?

Many engineers know:

```
Producer

↓

Topic

↓

Consumer
```

Very few understand:

- Why Kafka scales
- Why Kafka is fast
- Why partitions exist
- Why offsets exist
- Why consumer groups exist

Architects must understand these concepts.

---

# Complete Kafka Architecture

```text
                 Producer

                     │

          ───────────┼───────────

                     │

             Kafka Cluster

    ┌─────────┬─────────┬─────────┐

    │         │         │

 Broker1   Broker2   Broker3

    │         │         │

 Partitions Partitions Partitions

    │         │         │

      Replication Between Brokers

                     │

             Consumer Groups
```

Every component has a specific responsibility.

---

# Core Kafka Components

```text
Kafka

├── Broker

├── Topic

├── Partition

├── Producer

├── Consumer

├── Consumer Group

├── Offset

├── Replication

├── Controller

└── ZooKeeper / KRaft
```

We'll study each individually.

---

# 40. Kafka Broker

> **A Kafka Broker is a server responsible for storing partitions, receiving messages from producers, and serving messages to consumers.**

Think of a broker as:

A Kafka server.

---

# Single Broker

```text
Producer

↓

Broker

↓

Consumer
```

Suitable only for development.

---

# Production Cluster

```text
Producer

↓

Broker1

Broker2

Broker3

↓

Consumers
```

Production Kafka always uses:

Multiple brokers.

---

# Responsibilities

Broker performs:

- Store messages
- Replicate partitions
- Serve consumers
- Accept producer writes
- Handle leader election
- Maintain durability

---

# Why Multiple Brokers?

Imagine.

One broker.

Crashes.

Entire platform unavailable.

Instead.

```text
Broker1

Broker2

Broker3
```

Workload distributed.

High availability achieved.

---

# Broker Scaling

Need more capacity?

Add brokers.

Kafka automatically distributes partitions over time through reassignment.

Horizontal scaling.

---

# 41. Kafka Topics

> **A Topic is a logical stream of related events.**

Think of a topic as:

A named event channel.

---

# Examples

```
orders

payments

customers

inventory

shipments
```

Each topic stores:

Related events.

---

# Example

Topic.

```
orders
```

Contains.

```text
Order100

Order101

Order102
```

---

# Topic Naming

Good names.

```
orders

payments

inventory
```

Bad names.

```
topic1

abc

myqueue
```

Names should reflect business domains.

---

# Topic Characteristics

A topic is:

- Durable
- Ordered within partitions
- Append-only
- Distributed

---

# 42. Kafka Partitions

> **A Partition is an ordered, append-only log that stores a subset of a topic's messages.**

This is the secret behind Kafka scalability.

---

# Without Partitions

```text
Topic

↓

One Log
```

Only one server.

Limited throughput.

---

# With Partitions

```text
Topic

↓

Partition0

Partition1

Partition2
```

Three logs.

Parallel processing.

---

# Example

Orders Topic.

```text
Orders

├── P0

├── P1

└── P2
```

Messages distributed.

---

# Partition Ordering

Ordering guaranteed.

Only.

Within one partition.

---

Example.

Partition.

```
A

↓

B

↓

C
```

Always.

A → B → C

---

Across partitions.

No global ordering.

---

# Why Partitions?

Partitions enable:

- Horizontal scaling
- Parallel writes
- Parallel reads
- Fault tolerance

Without partitions,

Kafka would not scale.

---

# Partition Key

Producer chooses partition.

Usually using.

```
Customer ID

Order ID

User ID
```

All related events remain together.

---

# Example

```
Order123

↓

Partition1
```

Every update.

For Order123.

Same partition.

Order preserved.

---

# Bad Partition Key

Random.

Ordering lost.

---

# Good Partition Key

Business entity.

Ordering maintained.

---

# 43. Producers

> **A Producer publishes events to Kafka topics.**

Producer responsibility.

- Serialize event
- Choose partition
- Send message
- Retry failures
- Receive acknowledgement

---

# Workflow

```text
Producer

↓

Topic

↓

Partition
```

---

# Partition Selection

Producer can:

- Round Robin
- Hash Key
- Explicit Partition

---

# Round Robin

Messages evenly distributed.

---

# Hash Partitioning

Same key.

Same partition.

Preferred.

---

Example.

```
Customer101
```

Always.

Partition3.

---

# Producer ACK Modes

Kafka producer supports acknowledgement strategies.

---

## ACK = 0

Producer.

Does not wait.

Fastest.

Possible message loss.

---

## ACK = 1

Leader broker confirms.

Followers may still be behind.

Good balance.

---

## ACK = all

Leader waits.

All in-sync replicas acknowledge.

Highest durability.

Higher latency.

---

# Comparison

| ACK | Reliability | Latency |
|------|-------------|----------|
| 0 | Lowest | Lowest |
| 1 | Medium | Medium |
| all | Highest | Highest |

---

# 44. Consumers

> **Consumers read events from Kafka topics.**

Unlike queues.

Consumers decide.

Where to read.

Using offsets.

---

# Consumer Workflow

```text
Consumer

↓

Topic

↓

Partition

↓

Offset
```

---

# Poll Model

Kafka uses.

Pull.

Not push.

Consumers ask.

```
Give me new events.
```

---

# Why Pull?

Consumers process.

At their own speed.

No overload.

---

# Consumer Advantages

- Backpressure naturally handled
- Replay possible
- Independent speed
- Fault tolerant

---

# 45. Consumer Groups

> **A Consumer Group is a collection of consumers cooperating to process a topic.**

Consumer groups enable.

Horizontal scaling.

---

# Example

Orders Topic.

Three partitions.

Three consumers.

```text
P0

↓

Consumer1

---------------

P1

↓

Consumer2

---------------

P2

↓

Consumer3
```

Every partition.

One consumer.

---

# Why Consumer Groups?

Need.

Higher throughput.

Add consumers.

---

# Scaling Example

One consumer.

```
10K Messages/sec
```

Need.

```
100K/sec
```

Increase.

Consumers.

---

# Important Rule

Within one consumer group.

One partition.

One active consumer.

---

# Multiple Groups

Different applications.

Need same events.

Create.

Different groups.

---

Example.

```text
Orders Topic

↓

Inventory Group

↓

Analytics Group

↓

Billing Group
```

Each receives.

Entire stream.

---

# 46. Offsets

Every message.

Gets.

Unique offset.

Within partition.

---

Example.

```text
Offset

0

1

2

3

4
```

Consumers remember.

Last processed.

Offset.

---

# Offset Storage

Consumer commits.

```
Offset

1500
```

Restart.

Continue.

```
1501
```

---

# Offset Benefits

- Replay
- Recovery
- Reliability
- Exactly-once processing support

---

# 47. Kafka Replication

> **Replication creates copies of partitions across multiple brokers to ensure fault tolerance.**

Without replication.

Broker failure.

Means.

Data loss.

---

# Architecture

```text
Partition0

↓

Leader

↓

Follower

↓

Follower
```

Multiple copies.

---

# Replication Factor

Example.

```
RF = 3
```

Means.

Three copies.

Of every partition.

---

# Example

Partition.

```
Broker1

Leader

---------------

Broker2

Replica

---------------

Broker3

Replica
```

---

# Benefits

- High Availability
- Durability
- Fault tolerance

---

# 48. Leader and Followers

Each partition has.

One leader.

Multiple followers.

---

# Workflow

Producer.

Writes.

Only.

Leader.

---

Followers.

Replicate.

Leader.

---

Consumers.

Typically read.

Leader.

(Some deployments may enable follower reads depending on configuration and requirements.)

---

# Why?

Simpler consistency.

No conflicting writes.

---

# 49. ISR (In-Sync Replicas)

> **ISR is the set of replicas that are fully synchronized with the leader.**

Not every replica.

Is always current.

---

Example.

```text
Leader

↓

Replica A

↓

Replica B

↓

Replica C (Slow)
```

Replica C.

May leave.

ISR.

---

# Why ISR?

Producer.

Using.

```
ACK = all
```

Waits.

Only for.

ISR.

Not every replica.

Balances durability with availability.

---

# 50. Leader Election

Suppose.

Leader crashes.

Need.

New leader.

---

Example.

```text
Leader

↓

Crash

↓

Replica

↓

Promoted
```

System continues.

Automatically.

---

# Controller

Kafka cluster elects a controller node responsible for coordinating administrative tasks such as partition leadership changes.

Modern Kafka clusters use **KRaft** for this coordination, replacing the earlier dependency on ZooKeeper.

---

# ZooKeeper vs KRaft

Historically.

Kafka used.

```
ZooKeeper
```

For cluster metadata.

---

Modern Kafka.

Uses.

```
KRaft
```

Kafka Raft Metadata mode.

---

Benefits.

- Simpler deployment
- Fewer components
- Better scalability
- Easier operations

---

# Kafka Component Summary

| Component | Responsibility |
|------------|----------------|
| Broker | Stores partitions |
| Topic | Logical event stream |
| Partition | Ordered log |
| Producer | Writes events |
| Consumer | Reads events |
| Consumer Group | Parallel processing |
| Offset | Reading position |
| Leader | Accepts writes |
| Follower | Replication |
| ISR | Synchronized replicas |
| Controller (KRaft) | Cluster coordination |

---

# Common Mistakes

### One Partition

Limits throughput.

No parallel processing.

---

### Too Many Partitions

Creates unnecessary metadata and operational overhead.

Choose partition counts based on expected throughput and future growth.

---

### Poor Partition Key

Breaks ordering.

Creates hot partitions.

---

### ACK = 0 for Critical Data

Fast.

Unsafe.

Business risk.

---

### Replication Factor = 1

Single broker failure.

Data loss.

---

# Architect's Perspective

Kafka scales because it distributes work across:

- Brokers
- Partitions
- Consumer Groups

Rather than relying on one powerful server.

When designing Kafka systems, architects should focus on:

- Choosing the right partition key
- Selecting an appropriate partition count
- Configuring replication based on durability requirements
- Balancing throughput with ordering guarantees

Kafka's architecture demonstrates one of the core principles of distributed systems:

> **Scale horizontally by partitioning work, not by continuously increasing the size of a single machine.**

---

**End of Part 6A**

The next part will continue the Kafka deep dive with:

- **Log Segments**
- **Retention Policies**
- **Log Compaction**
- **Producer Idempotence**
- **Producer Transactions**
- **Partition Rebalancing**
- **Consumer Lag**
- **Throughput Optimization**
- **Capacity Planning**
- **Kafka Performance Tuning**
