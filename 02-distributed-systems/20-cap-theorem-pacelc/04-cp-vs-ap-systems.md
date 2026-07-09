
# 6. CP, AP & CA Systems in the Real World

One of the most common interview questions is:

> **Which databases are CP? Which are AP?**

Unfortunately.

The answer is not always.

Black and white.

Modern databases.

Often allow.

Configuration.

To move along.

The consistency-availability spectrum.

Nevertheless.

Each system.

Has a default philosophy.

Understanding these philosophies.

Is more important.

Than memorizing a table.

---

# Understanding the Landscape

```text
                     Distributed Systems

                             │

          ┌──────────────────┼──────────────────┐

          │                  │                  │

         CP                 AP                 CA*

                                          (*Single Node /
                                           No Partition)
```

---

# 6.1 CP Systems

> **CP systems choose Consistency over Availability whenever a network partition occurs.**

When communication.

Between replicas.

Breaks.

CP systems.

Prefer to reject requests.

Rather than risk.

Incorrect data.

---

# How CP Works

```text
Client

↓

Primary

↓

Network Partition

↓

Replica

↓

Reject Request
```

The system.

May become.

Temporarily unavailable.

But.

Data remains correct.

---

# Business Analogy

Imagine.

A bank.

Cannot contact.

Its central ledger.

Would you prefer.

It continues.

Dispensing money.

Or.

Temporarily refuses.

Withdrawals?

Most customers.

Prefer.

Temporary rejection.

Over incorrect balances.

---

# Typical Characteristics

CP systems generally provide.

- Strong consistency
- Linearizable reads
- Safe writes
- Leader election
- Quorum validation

---

# Advantages

- No stale reads
- No conflicting writes
- Easier reasoning
- Better correctness
- Strong transactional guarantees

---

# Disadvantages

- Reduced availability
- Higher latency
- Some requests rejected
- Failover delays

---

# Typical Business Domains

CP fits.

Systems where.

Incorrect data.

Is unacceptable.

Examples.

- Banking
- Financial Trading
- Airline Reservation
- Inventory
- Metadata Services
- Configuration Stores
- Distributed Locks

---

# Real Examples

## ZooKeeper

Purpose.

Cluster coordination.

Leader election.

Configuration.

---

Why CP?

Imagine.

Two leaders.

Existing simultaneously.

Cluster corruption.

Would occur.

ZooKeeper.

Would rather.

Reject requests.

Than allow.

Split brain.

---

## etcd

Kubernetes.

Stores.

Cluster state.

In etcd.

---

Question.

Should Kubernetes.

Have two different.

Views.

Of cluster configuration?

No.

Consistency.

Is mandatory.

---

Therefore.

etcd.

Chooses CP.

---

## HBase

HBase.

Prioritizes.

Strong consistency.

For.

Distributed storage.

---

## CockroachDB

CockroachDB.

Uses.

Consensus.

(Raft)

To preserve.

Strong consistency.

Across replicas.

It behaves.

Primarily.

As a CP database.

---

# CP Timeline

```text
Write

↓

Partition

↓

Cannot Reach Majority

↓

Reject

↓

Consistency Preserved
```

---

# Architect's Perspective

CP systems.

Protect.

Business correctness.

Even if.

Some users.

Temporarily.

Cannot access.

The system.

---

# 6.2 AP Systems

> **AP systems choose Availability over immediate Consistency whenever a network partition occurs.**

Instead of.

Rejecting requests.

They continue.

Serving clients.

Synchronization.

Occurs later.

---

# How AP Works

```text
Client

↓

Replica

↓

Partition

↓

Accept Request

↓

Synchronize Later
```

---

# Business Analogy

Imagine.

Instagram.

During.

A regional outage.

Should users.

Stop uploading photos?

Or.

Allow uploads.

And synchronize later?

Most users.

Prefer.

Continuous availability.

---

# Characteristics

AP systems.

Typically provide.

- Eventual consistency
- Conflict resolution
- High availability
- Multi-region writes
- Offline tolerance

---

# Advantages

- Excellent uptime
- Better user experience
- Lower perceived downtime
- Better resilience

---

# Disadvantages

- Stale reads
- Write conflicts
- Conflict resolution
- Temporary inconsistency

---

# Typical Business Domains

AP fits.

Systems where.

Temporary inconsistency.

Is acceptable.

Examples.

- Social Networks
- Product Catalogs
- Recommendations
- News Feeds
- Search
- Analytics
- DNS

---

# Real Examples

## Cassandra

One of the best-known.

AP databases.

---

How?

Every node.

Can accept writes.

During partitions.

Replication.

Occurs later.

Using.

Eventual consistency.

---

## DynamoDB

Originally inspired by.

Amazon Dynamo.

---

Dynamo philosophy.

```text
Always Accept Writes

↓

Resolve Conflicts Later
```

---

Modern DynamoDB.

Also allows.

Configurable consistency.

But its architecture.

Still reflects.

AP principles.

---

## Riak

Riak.

Was explicitly designed.

Around.

Availability.

And eventual consistency.

---

## CouchDB

Supports.

Offline clients.

Replication.

Conflict detection.

Later synchronization.

---

# AP Timeline

```text
Write

↓

Partition

↓

Accept

↓

Replicate Later

↓

Eventually Consistent
```

---

# Architect's Perspective

AP systems.

Optimize.

Business continuity.

Even during.

Infrastructure failures.

---

# 6.3 What About MongoDB?

MongoDB.

Is frequently asked.

In interviews.

The answer.

Is nuanced.

---

MongoDB.

Uses.

Replica Sets.

One node.

Acts as Primary.

Others.

Are Secondaries.

---

Normal Writes.

```text
Client

↓

Primary

↓

Replicas
```

---

If.

Primary.

Cannot contact.

A majority.

It steps down.

To prevent.

Split brain.

---

Default behavior.

Prioritizes.

Consistency.

Over availability.

---

Therefore.

MongoDB.

Is generally classified.

As.

CP.

---

However.

MongoDB also supports.

Different.

Read concerns.

Write concerns.

Read preferences.

Meaning.

Applications.

Can choose.

Different consistency levels.

---

# 6.4 What About Kafka?

Kafka.

Is another.

Frequently misunderstood.

System.

---

Kafka.

Orders.

Messages.

Using.

Partition Leaders.

---

Leader.

Accepts writes.

Followers replicate.

---

If leader.

Cannot reach.

Required replicas.

Depending on configuration.

Kafka.

May reject writes.

To preserve durability.

---

Kafka.

Leans.

Toward CP.

For durability.

Although.

Consumer availability.

May continue.

---

# 6.5 What About Redis?

Redis.

Depends.

On deployment mode.

---

Single Redis.

CAP.

Doesn't apply.

No distribution.

---

Redis Cluster.

Uses.

Replication.

And failover.

Consistency.

Depends.

On replication configuration.

---

Redis Sentinel.

May lose.

Recent writes.

During failover.

Depending on timing.

---

Therefore.

Redis.

Cannot simply be labeled.

CP or AP.

Configuration matters.

---

# 6.6 What About Google Spanner?

Google Spanner.

Uses.

TrueTime.

Consensus.

And synchronous replication.

---

Goal.

Strong global consistency.

---

Writes.

Require.

Consensus.

Across replicas.

---

Spanner.

Behaves primarily.

As.

CP.

---

# Summary Table

| System | Typical CAP Classification | Why |
|---------|---------------------------|-----|
| ZooKeeper | CP | Coordination requires correctness |
| etcd | CP | Kubernetes state must remain consistent |
| CockroachDB | CP | Raft consensus |
| Google Spanner | CP | Strong global consistency |
| HBase | CP | Consistent distributed storage |
| MongoDB (default Replica Set) | CP | Majority-based primary election |
| Kafka | Mostly CP | Leader + ISR replication for durability |
| Cassandra | AP | Accepts writes during partitions |
| Dynamo-style Systems | AP | Eventual consistency |
| Riak | AP | Always available writes |
| CouchDB | AP | Offline-first replication |
| Redis | Depends | Deployment and replication mode |

> **Important:** Modern databases are highly configurable. These classifications describe their **default architectural philosophy**, not an absolute rule.

---

# Why Modern Databases Blur the Lines

Early databases.

Made.

Fixed choices.

Modern systems.

Allow configuration.

---

Example.

Cassandra.

Allows.

```text
Consistency Level

ONE

QUORUM

ALL
```

Applications.

Choose.

The desired trade-off.

---

MongoDB.

Allows.

```text
Read Concern

↓

Local

↓

Majority

↓

Linearizable
```

Different workloads.

Choose.

Different guarantees.

---

This is why.

Modern architects.

Discuss.

Consistency levels.

Rather than.

Simply.

"CP or AP."

---

# Business Examples

## Banking

Requirement.

```text
Never lose money.
```

Choice.

CP.

---

## Instagram

Requirement.

```text
Never stop uploads.
```

Choice.

AP.

---

## E-commerce Product Catalog

Requirement.

```text
Products always visible.

Inventory can catch up.
```

Choice.

Usually AP.

---

## Airline Reservation

Requirement.

```text
Never sell the same seat twice.
```

Choice.

CP.

---

# Interview Tips

If asked.

> "Which is better, CP or AP?"

Never answer.

"CP."

Or.

"AP."

Instead answer.

> **Neither is universally better. The correct choice depends entirely on business requirements and the consequences of stale data versus rejected requests during a network partition.**

That answer.

Demonstrates.

Architectural thinking.

---

# Common Misconceptions

### Misconception 1

> Cassandra is always eventually consistent.

False.

It supports.

Configurable consistency levels.

---

### Misconception 2

> MongoDB is always AP.

False.

Default replica set behavior.

Prioritizes majority consistency.

---

### Misconception 3

> Every database is permanently CP or AP.

False.

Many modern databases.

Expose consistency settings.

Applications choose.

The appropriate trade-off.

---

### Misconception 4

> CAP classification alone is enough to choose a database.

False.

Architects must also consider.

- Latency
- Throughput
- Transactions
- Scalability
- Operational complexity
- Cost
- Ecosystem
- PACELC trade-offs

---

# Architect's Perspective

A mature architect never chooses a database simply because it is labeled **CP** or **AP**.

Instead, they begin with the business questions:

- What happens if users receive stale data?
- What happens if requests are rejected?
- How long can inconsistency be tolerated?
- What is the financial impact of each failure mode?

Only after understanding these answers should a database technology be selected.

CAP describes the trade-offs.

Business priorities determine which trade-off is acceptable.

The next section introduces **PACELC**, which extends CAP by answering an equally important question:

> **What trade-offs exist when there is *no* network partition?**

---

**End of Part 4**

The next part will cover:

- **Limitations of CAP**
- **Why CAP Is Not Enough**
- **Introduction to PACELC**
- **Else Latency or Consistency**
- **PACELC Examples**
- **Modern Distributed Database Design**
