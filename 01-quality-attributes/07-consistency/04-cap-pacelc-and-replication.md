
# 16. CAP Theorem

> **CAP Theorem is one of the most influential—and most misunderstood—principles in distributed systems.**

Many engineers incorrectly believe:

> "You can only choose two out of three."

This statement is an oversimplification.

The actual theorem states:

> **When a network partition occurs, a distributed system must choose between Consistency and Availability.**

Notice the important condition:

```
During a Network Partition
```

Not during normal operation.

---

# Why CAP Exists

Consider a distributed database.

```text
           Client

              │

    ┌─────────┴─────────┐

    ▼                   ▼

 Node A             Node B
```

Normally:

- Nodes communicate.
- Replication works.
- Data remains synchronized.

Now suppose:

The network connection fails.

```text
 Node A      X      Node B
```

Each node becomes isolated.

Now imagine:

Customer updates Node A.

Should Node B:

- Accept reads?
- Accept writes?
- Reject requests?

CAP explains these trade-offs.

---

# The Three Properties

---

# C — Consistency

Every client observes the same data.

After a successful write:

```text
Write

↓

Commit

↓

Every Read

↓

Latest Value
```

No stale data.

---

## Example

Inventory:

```
1 Laptop
```

Customer purchases it.

Immediately:

Every client sees:

```
0 Laptops
```

---

# A — Availability

Every request receives a response.

Even during failures.

Important:

The response may contain:

```
Old Data
```

Availability guarantees:

```
Response

NOT

Correctness
```

---

# Example

Replica unavailable.

Another replica still responds.

Customer receives:

```
Some Result
```

instead of:

```
Timeout
```

---

# P — Partition Tolerance

Network communication may fail.

The system continues operating.

Example:

```text
US Region

×

Europe Region
```

Each side continues independently.

Modern distributed systems assume partitions are inevitable.

---

# CAP Visualization

```text
             CAP

        /      |      \

Consistency  Availability  Partition

        \      |      /

 Distributed Systems
```

---

# Understanding Network Partitions

A partition means:

Servers are alive.

Network is broken.

Example:

```text
Node A

Alive

-------------

Network Failure

-------------

Node B

Alive
```

Each node continues running.

They simply cannot communicate.

---

# Life Before Partition

```text
Node A

↓

Node B

↓

Node C
```

Replication succeeds.

All nodes agree.

---

# During Partition

```text
Node A

×

Node B

↓

Node C
```

Questions arise:

Should Node A continue accepting writes?

Should Node B reject requests?

Should customers wait?

There is no perfect answer.

---

# Choosing Consistency

Suppose:

Customer writes:

```
Balance = ₹500
```

Network partition occurs.

Another customer reads from another region.

Consistency requires:

```
Wait

Until Agreement
```

If agreement cannot be reached:

Reject request.

Customers receive:

```
Unavailable
```

Consistency preserved.

Availability reduced.

---

# Choosing Availability

Instead,

allow every region to continue serving requests.

Customer receives:

```
Response Immediately
```

However,

different regions may temporarily disagree.

Availability preserved.

Consistency temporarily sacrificed.

---

# CAP Decision Matrix

| During Partition | Result |
|------------------|--------|
| Prioritize Consistency | Reject some requests |
| Prioritize Availability | Serve possibly stale data |

Partition tolerance is mandatory.

The real decision is:

```
Consistency

OR

Availability
```

---

# CP Systems

CP =

Consistency +

Partition Tolerance

---

Behavior:

```text
Partition

↓

Wait

↓

Consensus

↓

Return Latest Data
```

---

Advantages

- Correct data
- No stale reads

---

Disadvantages

- Some requests fail
- Higher latency

---

Typical Examples

- Google Spanner
- ZooKeeper
- etcd
- HBase

---

# AP Systems

AP =

Availability +

Partition Tolerance

---

Behavior

```text
Partition

↓

Continue Serving

↓

Synchronize Later
```

---

Advantages

- Excellent availability
- Fast responses
- Better user experience

---

Disadvantages

- Temporary inconsistency
- Conflict resolution required

---

Typical Examples

- Cassandra
- DynamoDB (eventual consistency mode)
- Riak

---

# Why CA Doesn't Exist

Many diagrams show:

```
CA
```

In practice,

distributed systems must tolerate network failures.

Without Partition Tolerance:

One broken cable stops the system.

Therefore,

modern distributed systems almost always assume:

```
P

is mandatory
```

The real architectural decision becomes:

```
CP

or

AP
```

---

# Real Examples

## Banking

Requirement:

```
Correct Balance
```

If communication fails:

Reject transactions.

Better than incorrect balances.

Banking generally prefers:

```
CP
```

---

## Social Media

Requirement:

Users should continue posting.

Temporary delays acceptable.

Social platforms generally prefer:

```
AP
```

---

## Shopping Cart

Temporary inconsistency acceptable.

Customer can continue shopping.

Eventually replicas synchronize.

---

## Inventory Reservation

Incorrect inventory causes financial loss.

Strong consistency usually preferred.

---

# CAP Example Timeline

Normal Operation

```text
Node A

↓

Node B

↓

Node C
```

All synchronized.

---

Network Partition

```text
Node A

×

Node B

↓

Node C
```

Option 1

Reject writes.

↓

Consistency maintained.

---

Option 2

Accept writes.

↓

Availability maintained.

---

# Common Misconceptions

### Myth

Choose any two.

**Reality**

The trade-off exists only during a partition.

During normal operation,

many systems provide:

- Consistency
- Availability

simultaneously.

---

### Myth

Partition Tolerance is optional.

**Reality**

Real networks experience failures.

Ignoring partitions creates fragile systems.

---

### Myth

Availability always means correct responses.

**Reality**

Availability guarantees responses,

not necessarily the newest data.

---

# 17. PACELC Theorem

CAP explains behavior during partitions.

But:

What happens when there is **no partition?**

PACELC answers that question.

---

# PACELC Definition

```
If

Partition

↓

Choose

Availability

or

Consistency

Else

↓

Choose

Latency

or

Consistency
```

Hence:

```
PA/EL

C
```

---

# PACELC Diagram

```text
Partition?

↓

YES

↓

Availability

or

Consistency

↓

NO

↓

Latency

or

Consistency
```

---

# Why PACELC Matters

Most production systems operate:

```
Without

Network Partitions
```

Architects still make trade-offs.

Example:

Strong consistency often requires:

- Additional coordination
- More network round trips

Result:

Higher latency.

---

# Example

Suppose:

User updates profile.

Strong consistency:

```text
Primary

↓

Replica

↓

Replica

↓

Acknowledgement

↓

Response
```

Latency increases.

---

Eventually consistent system:

```text
Primary

↓

Response

↓

Replicas Later
```

Lower latency.

---

# PACELC Trade-offs

| Choice | Benefit | Cost |
|----------|----------|------|
| Consistency | Correctness | Higher latency |
| Latency | Faster response | Possible stale reads |

---

# CAP vs PACELC

| CAP | PACELC |
|------|---------|
| During partitions | During normal operation |
| Consistency vs Availability | Consistency vs Latency |
| Failure-focused | Everyday architecture |

---

# Architect's Perspective

Modern distributed systems are better understood through **PACELC** than CAP alone.

Ask two separate questions:

1. **If the network partitions, do we prioritize consistency or availability?**
2. **When the network is healthy, do we prioritize consistency or latency?**

These two decisions shape the behavior of every distributed data platform.

---

# 18. Replication Architectures

Consistency depends heavily on how writes propagate.

Three major architectures exist:

```text
Leader-Based

↓

Multi-Leader

↓

Leaderless
```

Each provides different consistency guarantees.

---

# 18.1 Leader-Based Replication

One node accepts all writes.

```text
          Client

             │

             ▼

          Leader

         /   |   \

 Replica Replica Replica
```

Reads may come from:

- Leader
- Replicas

depending on requirements.

---

## Write Flow

```text
Client

↓

Leader

↓

Replicas

↓

Acknowledgement
```

Advantages:

- Simple conflict resolution
- Predictable ordering
- Easier strong consistency

---

Disadvantages:

- Leader becomes bottleneck
- Leader election required
- Write scalability limited

---

Typical Databases

- PostgreSQL
- MySQL
- SQL Server
- MongoDB Replica Sets

---

# 18.2 Multi-Leader Replication

Multiple regions accept writes.

```text
US Leader

Europe Leader

Asia Leader
```

Each leader replicates to others.

---

Advantages

- Better regional latency
- Regional independence
- Higher write availability

---

Challenges

Conflicts.

Example:

US:

```
Price = ₹100
```

Europe:

```
Price = ₹120
```

Same product.

Same time.

Conflict resolution required.

---

Typical Use Cases

- Multi-region SaaS
- Collaborative editing
- Global applications

---

# 18.3 Leaderless Replication

No primary node.

Client writes to multiple replicas.

```text
Client

↓

Node A

↓

Node B

↓

Node C
```

Success depends on:

Quorum.

---

Advantages

- No leader bottleneck
- High availability
- Better partition tolerance

---

Disadvantages

- Conflict resolution
- Read repair
- More complex algorithms

---

Typical Databases

- Cassandra
- DynamoDB
- Riak

---

**End of Part 4**

The next section will cover:

- **Quorum Reads & Writes**
- **Read Repair**
- **Hinted Handoff**
- **Vector Clocks**
- **Conflict Resolution**
- **Consensus Algorithms (Raft & Paxos)**
- **Leader Election**
- **Split-Brain**
- **Distributed Coordination**
- **Consistency Decision Matrix**
