
# 8. PACELC Classification of Modern Databases

Now that we understand.

PACELC.

Let's see.

How real-world databases.

Fit into this model.

Remember.

PACELC asks two questions.

```text
IF Partition

↓

Availability

OR

Consistency

ELSE

Latency

OR

Consistency
```

Every distributed database.

Answers both.

---

# PACELC Categories

There are four major categories.

```text
PACELC

│

├── PA / EL

├── PA / EC

├── PC / EL

└── PC / EC
```

Let's understand.

Each one.

---

# 8.1 PA / EL

## Partition → Availability

## Else → Latency

> **During partitions, prioritize Availability. During normal operation, prioritize Low Latency.**

This category.

Provides.

The fastest.

User experience.

---

# Characteristics

During partition.

```text
Accept Requests

↓

Synchronize Later
```

---

During normal operation.

```text
Nearest Replica

↓

Immediate Response
```

No waiting.

For global agreement.

---

# Business Analogy

Imagine.

Instagram.

A customer uploads.

A photo.

Should the upload.

Wait.

For confirmation.

From every continent?

No.

Users expect.

Instant response.

Replication.

Can happen later.

---

# Advantages

- Lowest latency
- Excellent availability
- Great user experience
- Massive scalability
- High throughput

---

# Disadvantages

- Stale reads
- Eventual consistency
- Conflict resolution
- Temporary divergence

---

# Typical Systems

- Cassandra (default philosophy)
- Amazon Dynamo
- Riak
- CouchDB

---

# Timeline

```text
Client

↓

Nearest Replica

↓

Success

↓

Replication Later
```

Fast.

Simple.

Highly available.

---

# Suitable Business Domains

- Social Media
- Recommendations
- Product Catalog
- Search
- Analytics
- User Preferences

---

# Architect's Perspective

Choose PA/EL.

When.

Business continuity.

And.

User experience.

Matter more.

Than.

Immediate consistency.

---

# 8.2 PA / EC

## Partition → Availability

## Else → Consistency

> **Remain available during partitions, but under normal conditions synchronize replicas before acknowledging writes.**

This category.

Still prefers.

Availability.

During failures.

But.

When the network.

Is healthy.

It waits.

For stronger consistency.

---

# Example

Normal operation.

```text
Client

↓

Replica A

↓

Replica B

↓

Replica C

↓

Acknowledgement
```

Higher latency.

Better consistency.

---

Partition.

```text
Client

↓

Local Replica

↓

Accept

↓

Synchronize Later
```

Availability preserved.

---

# Advantages

- High availability
- Better consistency than PA/EL
- Fewer conflicts

---

# Disadvantages

- Higher latency
- More coordination
- Reduced throughput

---

# Typical Usage

Many modern databases.

Can be configured.

Toward.

PA/EC.

Using.

Quorum writes.

Or stronger.

Consistency levels.

---

# Example

Cassandra.

Configured.

With.

```text
QUORUM

Reads

+

QUORUM

Writes
```

Moves closer.

Toward.

PA/EC behavior.

---

# Architect's Perspective

Useful.

When.

Business wants.

Availability.

But also.

Stronger guarantees.

During normal operation.

---

# 8.3 PC / EL

## Partition → Consistency

## Else → Latency

This category.

Is relatively uncommon.

---

Why?

Because.

If a system.

Already prioritizes.

Consistency.

During failures.

Most architects.

Also prefer.

Consistency.

During normal operation.

---

Nevertheless.

Certain specialized systems.

Can behave.

Closer.

To PC/EL.

For.

Read-heavy workloads.

Where local reads.

Are acceptable.

But writes.

Require consensus.

---

# Concept

Partition.

```text
Reject Requests
```

---

Normal.

```text
Serve Local Reads

↓

Low Latency
```

---

This category.

Appears.

Less frequently.

Than the others.

---

# 8.4 PC / EC

## Partition → Consistency

## Else → Consistency

> **Always prioritize correctness, even at the cost of latency and temporary unavailability.**

This is.

The strongest.

Consistency model.

---

# Behavior

During partition.

```text
Reject Request

↓

Maintain Correctness
```

---

During normal operation.

```text
Wait For Consensus

↓

Acknowledge
```

Every write.

Requires.

Coordination.

---

# Timeline

```text
Client

↓

Leader

↓

Replica A

↓

Replica B

↓

Replica C

↓

Commit

↓

Response
```

Higher latency.

Maximum correctness.

---

# Advantages

- Strong consistency
- Linearizable reads
- No stale data
- Predictable behavior
- Easier reasoning

---

# Disadvantages

- Highest latency
- More coordination
- Lower throughput
- Temporary request failures

---

# Typical Systems

- Google Spanner
- CockroachDB
- ZooKeeper
- etcd
- FoundationDB

---

# Business Domains

- Banking
- Payments
- Financial Trading
- Distributed Locks
- Metadata
- Configuration Stores

---

# Architect's Perspective

If.

Returning incorrect data.

Costs.

Millions of dollars.

Choose.

PC/EC.

---

# PACELC Comparison Table

| Category | During Partition | During Normal Operation | Typical Goal |
|----------|------------------|-------------------------|--------------|
| PA/EL | Availability | Low Latency | User Experience |
| PA/EC | Availability | Stronger Consistency | Balanced |
| PC/EL | Consistency | Lower Latency | Specialized Workloads |
| PC/EC | Consistency | Strong Consistency | Correctness |

---

# Real Database Classification

| Database | Approximate PACELC Model | Reason |
|-----------|--------------------------|--------|
| Cassandra | PA/EL | High availability, local writes |
| Dynamo | PA/EL | Availability-first design |
| Riak | PA/EL | Eventual consistency |
| CouchDB | PA/EL | Offline-first replication |
| Cassandra (QUORUM) | PA/EC | Higher consistency with quorum |
| MongoDB | PC/EC (default) | Majority writes and elections |
| CockroachDB | PC/EC | Raft consensus |
| Google Spanner | PC/EC | TrueTime + synchronous replication |
| ZooKeeper | PC/EC | Coordination correctness |
| etcd | PC/EC | Cluster state consistency |

> **Note:** These are architectural tendencies. Most modern databases expose configuration options that can shift behavior depending on workload and deployment.

---

# Choosing the Right PACELC Model

## Banking

Requirement.

```text
Never show wrong balance.
```

Choice.

```text
PC / EC
```

---

## Social Media

Requirement.

```text
Never stop serving users.
```

Choice.

```text
PA / EL
```

---

## E-Commerce Catalog

Requirement.

```text
Fast browsing.

Inventory catches up.
```

Choice.

```text
PA / EL

or

PA / EC
```

Depending.

On inventory strategy.

---

## Distributed Configuration

Requirement.

```text
All servers.

Must use.

Exactly the same configuration.
```

Choice.

```text
PC / EC
```

---

# Interview Scenario

**Question**

> Why doesn't Google Spanner choose PA/EL?

**Answer**

Because Google's primary objective for Spanner is **globally consistent SQL transactions**.

Allowing writes to proceed independently during a partition could produce conflicting transaction histories.

Spanner therefore prefers **correctness over availability** during partitions and **consistency over latency** during normal operation, making it a classic **PC/EC** system.

---

# CAP vs PACELC Decision Tree

```text
              Is There A Network Partition?

                    Yes              No

                     │                │

              Availability?     Low Latency?

                 or                 or

             Consistency      Consistency
```

CAP answers.

The left branch.

PACELC.

Answers both.

---

# Common Misconceptions

### Misconception 1

> Every AP system is PA/EL.

False.

Some AP systems.

Choose.

Higher consistency.

During normal operation.

---

### Misconception 2

> PC/EC systems are always slow.

False.

Reads can still be fast.

The latency cost.

Is primarily on.

Coordination.

And writes.

---

### Misconception 3

> PACELC categories are fixed forever.

False.

Modern databases.

Allow applications.

To configure.

Consistency levels.

Quorums.

Read preferences.

And replication behavior.

---

### Misconception 4

> Lower latency is always better.

False.

Low latency.

Is valuable.

Only if.

It still satisfies.

Business correctness.

---

# Architect's Decision Framework

When evaluating a distributed database, ask:

| Question | Why It Matters |
|----------|----------------|
| What happens during a network partition? | Determines the CAP behavior |
| During normal operation, do we value latency or stronger consistency? | Determines the PACELC behavior |
| Can users tolerate stale reads? | Guides consistency level |
| Is write latency acceptable? | Impacts user experience |
| What is the cost of incorrect data? | Drives CP vs AP decisions |
| How global is the deployment? | Affects replication strategy |

---

# Architect's Perspective

PACELC teaches an important lesson:

> **Distributed systems make trade-offs even when everything is working perfectly.**

Many engineers focus only on failures.

Experienced architects spend just as much time optimizing **normal operation**, because that is where users spend almost all of their time.

The best architecture is not the one with the strongest guarantees.

It is the one that delivers the **minimum guarantees required by the business** while maximizing performance, scalability, and user experience.

The next section will cover **CAP & PACELC in System Design Interviews**, common mistakes, design strategies, and practical decision-making frameworks.

---

**End of Part 6**

The next part will cover:

- **CAP & PACELC in System Design Interviews**
- **Common Interview Questions**
- **Architecture Decision Framework**
- **Real-world Design Scenarios**
- **Common Anti-patterns**
- **Production Best Practices**
- **Chapter Summary**
