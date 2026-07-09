
# 7. Quorum Replication (N, R, W)

> **Quorum Replication is a technique that balances Consistency, Availability, and Performance by requiring only a subset of replicas to participate in read and write operations.**

This is one of.

The most important.

Concepts.

In distributed databases.

If you understand.

Quorums.

You'll understand.

How systems like.

- Cassandra
- DynamoDB
- Riak
- ScyllaDB

Achieve.

High Availability.

Without sacrificing.

Too much consistency.

---

# Why Do We Need Quorums?

Imagine.

You have.

Three replicas.

```text
Replica A

Replica B

Replica C
```

Customer updates.

Address.

Question.

Should the write.

Wait.

For all three?

If yes.

The system.

Becomes slower.

---

Alternative.

Should.

One replica.

Be enough?

If yes.

The system.

May lose data.

---

There must be.

A balance.

That balance.

Is called.

A **Quorum**.

---

# What is a Quorum?

> **A Quorum is the minimum number of replicas that must participate in an operation before it is considered successful.**

Instead of requiring.

Every replica.

We require.

Only enough replicas.

To safely.

Proceed.

---

# The Three Parameters

Leaderless databases.

Usually define.

Three values.

```text
N

R

W
```

Everything.

In quorum replication.

Starts here.

---

# N — Replication Factor

> **N is the total number of replicas that store a piece of data.**

Example.

```text
N = 3
```

Means.

Every record.

Exists.

On.

Three nodes.

---

Example.

```text
Customer Record

↓

Node A

↓

Node B

↓

Node C
```

Three copies.

Exist.

---

# R — Read Quorum

> **R is the minimum number of replicas that must respond to a read request.**

Example.

```text
R = 2
```

The database.

Reads.

From.

Two replicas.

Then.

Determines.

The latest value.

---

# W — Write Quorum

> **W is the minimum number of replicas that must acknowledge a write before it is considered successful.**

Example.

```text
W = 2
```

The client.

Receives success.

Only after.

Two replicas.

Store the update.

---

# Visual Example

```text
N = 3

Replica A

Replica B

Replica C
```

Write.

```text
W = 2
```

Need.

Any two.

Acknowledgements.

---

Read.

```text
R = 2
```

Need.

Any two.

Responses.

---

# Why Not Use All Replicas?

Suppose.

```text
N = 5
```

Waiting.

For all five.

Would increase.

Latency.

And.

Reduce availability.

If one replica.

Fails.

The entire write.

Would fail.

Quorums.

Avoid this.

---

# The Golden Rule

The most important formula.

In quorum replication.

Is.

```text
R + W > N
```

This rule.

Ensures.

Reads.

Always overlap.

With.

Recent writes.

---

# Why Does This Work?

Suppose.

```text
N = 3

R = 2

W = 2
```

Calculate.

```text
R + W

=

4

>

3
```

Satisfied.

---

Question.

Can.

The read.

Completely miss.

The latest write?

Impossible.

At least.

One replica.

Must contain.

The latest data.

---

# Example

Three replicas.

```text
A

B

C
```

Write.

Stored on.

```text
A

B
```

Later.

Read.

Queries.

```text
B

C
```

Notice.

Replica B.

Appears.

In both operations.

Overlap exists.

The latest write.

Can be found.

---

# Visual Proof

Write Quorum.

```text
A

B
```

Read Quorum.

```text
B

C
```

Common Replica.

```text
B
```

Therefore.

The read.

Cannot completely miss.

The latest version.

---

# What Happens If

R + W ≤ N ?

Suppose.

```text
N = 3

R = 1

W = 1
```

Now.

```text
1 + 1

=

2

≤

3
```

Danger.

---

Example.

Write.

Only reaches.

Replica A.

---

Later.

Read.

Uses.

Replica C.

Replica C.

Never received.

The update.

Customer reads.

Old data.

---

Timeline.

```text
Write

↓

Replica A

↓

Success

↓

Read

↓

Replica C

↓

Old Value
```

Stale read.

Occurs.

---

# Example Configurations

## Stronger Consistency

```text
N = 3

R = 2

W = 2
```

Rule satisfied.

Good consistency.

---

## Fast Writes

```text
N = 3

R = 3

W = 1
```

Writes.

Very fast.

Reads.

Slower.

---

## Fast Reads

```text
N = 3

R = 1

W = 3
```

Reads.

Very fast.

Writes.

Slower.

---

## High Availability

```text
N = 3

R = 1

W = 1
```

Very available.

Poor consistency.

---

# Trade-offs

| Configuration | Read Latency | Write Latency | Consistency |
|---------------|-------------:|--------------:|-------------|
| R=1, W=1 | Lowest | Lowest | Weak |
| R=2, W=2 | Medium | Medium | Strong |
| R=3, W=3 | Highest | Highest | Strongest |
| R=1, W=3 | Lowest | Highest | Strong Reads |
| R=3, W=1 | Highest | Lowest | Strong Writes |

---

# Business Examples

## Banking

```text
N = 3

R = 2

W = 2
```

Prioritize.

Correctness.

---

## Social Media Feed

```text
N = 3

R = 1

W = 1
```

Prioritize.

Availability.

And speed.

---

## Product Catalog

```text
N = 3

R = 1

W = 2
```

Fast browsing.

Reasonable consistency.

---

# Quorum Doesn't Mean Perfect Consistency

Even if.

```text
R + W > N
```

Temporary inconsistencies.

Can still occur.

Because.

- Network delays
- Clock skew
- Failed replicas
- Concurrent writes

Additional mechanisms.

Are required.

---

# Read Repair

Suppose.

Three replicas.

Contain.

```text
A

Version 5

B

Version 5

C

Version 4
```

Customer reads.

From.

All three.

System detects.

Replica C.

Is outdated.

Automatically.

Updates.

Replica C.

---

# Timeline

```text
Read

↓

Version Comparison

↓

Outdated Replica Found

↓

Automatic Update
```

This process.

Is called.

**Read Repair.**

---

# Hinted Handoff

Suppose.

Replica C.

Is offline.

Write occurs.

```text
A

Updated

↓

B

Updated

↓

C

Offline
```

Another node.

Temporarily stores.

A hint.

When.

Replica C.

Returns.

The hint.

Is delivered.

This mechanism.

Is called.

**Hinted Handoff.**

---

# Anti-Entropy

Sometimes.

Replicas diverge.

Without any client reads.

Background processes.

Periodically compare.

Replica data.

And synchronize.

Differences.

This process.

Is called.

**Anti-Entropy Repair.**

---

# Example

```text
Replica A

Version 10

Replica B

Version 10

Replica C

Version 8
```

Background repair.

Synchronizes.

Replica C.

Without waiting.

For a client request.

---

# Real Database Examples

## Cassandra

Supports.

```text
N

R

W
```

Applications choose.

Consistency level.

For every query.

Examples.

```text
ONE

QUORUM

ALL

LOCAL_QUORUM
```

---

## DynamoDB

Internally.

Uses quorum-like.

Replication.

Combined with.

Leader-based coordination.

Applications can choose.

Eventually consistent.

Or.

Strongly consistent.

Reads.

---

## Riak

Built.

Around quorum replication.

Read repair.

And anti-entropy.

---

# Quorum vs Synchronous Replication

| Feature | Synchronous Replication | Quorum Replication |
|----------|-------------------------|--------------------|
| Waits for every replica | Often Yes | No |
| Waits for subset | Usually No | Yes |
| Latency | Higher | Lower |
| Availability | Lower | Higher |
| Scalability | Moderate | Excellent |

---

# Common Misconceptions

### Misconception 1

> Quorum means every replica participates.

False.

Only.

The required quorum.

Must participate.

---

### Misconception 2

> R + W > N guarantees perfect consistency.

False.

It improves consistency.

But concurrent writes.

And partitions.

Still require.

Conflict resolution.

---

### Misconception 3

> Read Repair happens immediately after every write.

False.

Read Repair.

Occurs.

During.

Read operations.

---

### Misconception 4

> Anti-Entropy and Read Repair are the same.

False.

Read Repair.

Occurs.

While serving.

Client reads.

Anti-Entropy.

Runs.

In the background.

Regardless.

Of client activity.

---

# Architect's Decision Framework

Before configuring quorum settings, ask:

| Question | Why It Matters |
|----------|----------------|
| Is stale data acceptable? | Determines quorum strength |
| Are reads more frequent than writes? | Optimize R accordingly |
| Are writes more frequent than reads? | Optimize W accordingly |
| What is the acceptable latency? | Larger quorums increase latency |
| How many replica failures must be tolerated? | Influences N, R, and W |
| Can conflicts occur? | Determines need for repair mechanisms |

---

# Architect's Perspective

Quorum replication is one of the most elegant ideas in distributed systems.

Instead of forcing every replica to participate, it relies on **overlapping subsets** of replicas to balance:

- Consistency
- Availability
- Latency
- Fault tolerance

The famous rule:

> **R + W > N**

is not just a mathematical formula.

It is the principle that allows systems like Cassandra and Dynamo to remain highly available while still providing strong consistency guarantees when configured appropriately.

The next section explores **Conflict Detection & Resolution**, where we'll learn what happens when replicas disagree and how distributed systems converge back to a consistent state.

---

**End of Part 6**

The next part will cover:

- **Conflict Detection**
- **Conflict Resolution**
- **Version Vectors**
- **Last Write Wins**
- **Operational Transformation**
- **CRDTs**
- **Real-world Conflict Handling Strategies**
