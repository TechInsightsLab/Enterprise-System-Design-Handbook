
# 5. Replication Synchronization Models

So far.

We learned.

How databases.

Organize replicas.

Now.

Let's answer.

A much more important question.

> **When should the Primary tell the client that a write is successful?**

Should it wait.

Until.

Every replica.

Has stored the data?

Or.

Should it respond immediately?

This decision.

Creates three major.

Replication synchronization models.

---

# The Three Synchronization Models

```text
Replication

│

├── Synchronous Replication

├── Asynchronous Replication

└── Semi-Synchronous Replication
```

Nearly every.

Distributed database.

Uses one of these.

Or a variation.

---

# Why Synchronization Matters

Imagine.

A customer.

Transfers.

₹50,000.

To another account.

The Primary.

Stores the transaction.

Question.

Should the customer.

Receive.

"Transfer Successful"

Immediately?

Or.

Only after.

Other replicas.

Also store.

The transaction?

This decision.

Determines.

- Durability
- Availability
- Latency
- Risk of data loss

---

# Write Lifecycle

Every write.

Typically follows.

This sequence.

```text
Client

↓

Primary

↓

Replication

↓

Replica

↓

Acknowledgement
```

The only difference.

Between the three models.

Is.

**When the acknowledgement is returned.**

---

# 5.1 Synchronous Replication

> **The Primary waits until one or more replicas confirm that the data has been stored before acknowledging the client.**

This is.

The safest.

Replication model.

---

# Write Flow

```text
Client

↓

Primary

↓

Replica A

↓

Replica B

↓

Acknowledgement

↓

Client Success
```

Notice.

The client.

Waits.

Until replication.

Completes.

---

# Timeline

```text
Write Request

↓

Primary Write

↓

Replica 1

↓

Replica 2

↓

ACK Received

↓

Client Response
```

---

# Example

Customer.

Deposits.

₹10,000.

```text
Primary

↓

Replica 1

↓

Replica 2

↓

Success
```

Only after.

Every required replica.

Confirms.

Does the customer.

Receive.

```text
Transfer Successful
```

---

# Advantages

Synchronous replication.

Provides.

- Strong consistency
- Excellent durability
- Minimal data loss
- Predictable failover
- Easier recovery

---

# Disadvantages

The Primary.

Must wait.

For replicas.

Therefore.

- Higher latency
- Lower throughput
- Slower writes
- Reduced availability if replicas are unreachable

---

# Failure Example

Suppose.

Replica 2.

Becomes unreachable.

```text
Primary

↓

Replica 1

↓

Replica 2 ❌
```

Question.

Should the write.

Succeed?

Depends.

On.

Replication policy.

If.

Replica 2.

Is required.

The write.

May fail.

---

# Business Use Cases

Choose.

Synchronous replication.

When.

Incorrect data.

Is unacceptable.

Examples.

- Banking
- Payments
- Stock Trading
- Airline Reservation
- Distributed Configuration
- Metadata Services

---

# Typical Systems

Examples.

Include.

- Google Spanner
- CockroachDB
- PostgreSQL (synchronous mode)
- SQL Server Always On
- Oracle Data Guard (Maximum Protection)

---

# Architect's Perspective

Synchronous replication.

Optimizes.

Correctness.

At the expense of.

Latency.

---

# 5.2 Asynchronous Replication

> **The Primary acknowledges the client immediately after storing the write locally. Replication happens later in the background.**

This is.

The fastest.

Replication model.

---

# Write Flow

```text
Client

↓

Primary

↓

Client Success

↓

Background Replication

↓

Replica A

↓

Replica B
```

Notice.

The client.

Does not wait.

---

# Timeline

```text
Write Request

↓

Primary Write

↓

Client Success

↓

Replication Later

↓

Replica Updated
```

---

# Example

Customer.

Updates.

Shipping address.

The Primary.

Immediately returns.

```text
Address Updated Successfully
```

Seconds later.

Replicas.

Receive the change.

---

# Why Use Asynchronous Replication?

Because.

Users prefer.

Fast responses.

Most businesses.

Can tolerate.

A few seconds.

Of inconsistency.

---

# Advantages

Asynchronous replication.

Provides.

- Very low latency
- High throughput
- Better availability
- Better scalability
- Excellent user experience

---

# Disadvantages

Because.

Replication happens later.

The system risks.

- Replication lag
- Stale reads
- Lost writes
- Replica inconsistency

---

# Failure Scenario

Suppose.

```text
Client

↓

Primary

↓

Success Returned

↓

💥 Primary Crashes

↓

Replication Never Happened
```

Question.

What happened?

The client.

Believes.

The write succeeded.

Replicas.

Never received it.

The write.

Is permanently lost.

---

# Business Example

Instagram.

User updates.

Profile picture.

Primary responds.

Immediately.

Replication.

Occurs later.

A brief delay.

Is acceptable.

---

# Typical Systems

Examples.

Include.

- MySQL (default)
- PostgreSQL (default)
- MongoDB (secondary replication)
- Redis Replication
- Elasticsearch
- Cassandra (depending on consistency level)

---

# Architect's Perspective

Asynchronous replication.

Optimizes.

Performance.

At the cost of.

Possible data loss.

---

# 5.3 Semi-Synchronous Replication

> **The Primary waits for acknowledgement from at least one replica before responding to the client. Remaining replicas update asynchronously.**

Semi-Synchronous.

Attempts.

To balance.

Safety.

And performance.

---

# Write Flow

```text
Client

↓

Primary

↓

Replica A

↓

ACK

↓

Client Success

↓

Replica B

↓

Replica C
```

Only.

One replica.

Must confirm.

---

# Timeline

```text
Write

↓

Primary

↓

Replica A

↓

ACK

↓

Client Success

↓

Other Replicas

↓

Eventually Updated
```

---

# Why Use Semi-Synchronous?

It greatly reduces.

The probability.

Of data loss.

Without waiting.

For every replica.

---

# Example

Suppose.

Three replicas exist.

```text
Primary

↓

Replica A

↓

Replica B

↓

Replica C
```

Primary waits.

Only for.

Replica A.

Replicas B and C.

Synchronize later.

---

# Advantages

Semi-Synchronous.

Provides.

- Better durability than asynchronous
- Lower latency than synchronous
- Reduced write loss
- Better failover readiness

---

# Disadvantages

Still.

Not perfect.

If.

Primary.

And.

Replica A.

Fail together.

Some writes.

May still be lost.

---

# Typical Systems

Examples.

Include.

- MySQL Semi-Synchronous Replication
- MariaDB
- Oracle GoldenGate
- Various enterprise HA deployments

---

# Comparison

| Feature | Synchronous | Semi-Synchronous | Asynchronous |
|----------|-------------|------------------|--------------|
| Client waits for replicas | All required replicas | At least one replica | No |
| Write latency | Highest | Medium | Lowest |
| Throughput | Lowest | Medium | Highest |
| Risk of data loss | Very Low | Low | Higher |
| Consistency | Strong | Stronger than Async | Eventual |
| Availability | Lower | Medium | High |

---

# Visual Comparison

## Synchronous

```text
Primary

↓

Replica A

↓

Replica B

↓

ACK

↓

Client
```

---

## Semi-Synchronous

```text
Primary

↓

Replica A

↓

ACK

↓

Client

↓

Replica B

↓

Replica C
```

---

## Asynchronous

```text
Primary

↓

ACK

↓

Client

↓

Replica A

↓

Replica B
```

---

# Choosing the Right Synchronization Model

| Business Requirement | Recommended Model |
|----------------------|-------------------|
| Banking | Synchronous |
| Financial Trading | Synchronous |
| Inventory | Semi-Synchronous |
| E-Commerce Orders | Semi-Synchronous |
| Product Catalog | Asynchronous |
| Social Media | Asynchronous |
| Analytics | Asynchronous |
| Logging | Asynchronous |

---

# Real-World Example

Imagine.

An online shopping platform.

Different services.

Can use different.

Replication models.

```text
Payment Database

↓

Synchronous

------------------------

Order Database

↓

Semi-Synchronous

------------------------

Product Catalog

↓

Asynchronous

------------------------

Analytics

↓

Asynchronous
```

Large systems.

Rarely use.

One model.

Everywhere.

---

# Common Misconceptions

### Misconception 1

> Synchronous replication means zero latency.

False.

Waiting for replicas.

Always increases.

Write latency.

---

### Misconception 2

> Asynchronous replication is unsafe.

Not necessarily.

It is appropriate.

For workloads.

Where temporary inconsistency.

Or small write-loss windows.

Are acceptable.

---

### Misconception 3

> Semi-Synchronous guarantees zero data loss.

False.

It reduces risk.

But does not eliminate it.

---

### Misconception 4

> Every replica must acknowledge every write.

False.

Many systems.

Require only.

A quorum.

Or a subset.

Of replicas.

---

# Architect's Decision Framework

Before selecting a synchronization model, ask:

| Question | Why It Matters |
|----------|----------------|
| Can the business tolerate losing the most recent write? | Determines durability requirements |
| How much write latency is acceptable? | Influences user experience |
| Is every write financially critical? | Suggests synchronous replication |
| Are reads far more frequent than writes? | May justify asynchronous replication |
| What is the Recovery Point Objective (RPO)? | Drives replication guarantees |
| What is the acceptable replication lag? | Determines consistency expectations |

---

# Architect's Perspective

Replication synchronization is a classic engineering trade-off.

You cannot simultaneously achieve:

- Zero latency
- Zero data loss
- Infinite availability

Every synchronization model chooses a different balance.

Experienced architects don't ask:

> **"Which replication model is best?"**

Instead they ask:

> **"What level of durability, latency, and availability does the business require?"**

The answer determines whether synchronous, asynchronous, or semi-synchronous replication is appropriate.

The next section explores one of the biggest operational challenges introduced by asynchronous replication:

**Replication Lag**, its causes, its business impact, and techniques to minimize or hide it.

---

**End of Part 4**

The next part will cover:

- **Replication Lag**
- **Read-after-Write Consistency**
- **Monotonic Reads**
- **Read-your-Writes**
- **Session Consistency**
- **Sticky Sessions**
- **Business Impact of Stale Reads**
