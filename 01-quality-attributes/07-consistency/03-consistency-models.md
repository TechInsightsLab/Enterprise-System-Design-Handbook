
# 15. Consistency Models

> **A consistency model defines the rules that determine when different clients observe updates in a distributed system.**

Every distributed database must answer one fundamental question:

> **After a write completes, when can another client safely read the updated value?**

Different systems answer this differently.

Some guarantee:

```
Immediately
```

Others guarantee:

```
Eventually
```

Some provide guarantees only:

- Within a session
- After majority agreement
- Under certain conditions

There is **no universally best consistency model**.

The correct model depends entirely on business requirements.

---

# Consistency Spectrum

```text
Weak

↓

Eventual

↓

Read-after-Write

↓

Monotonic Reads

↓

Monotonic Writes

↓

Session

↓

Consistent Prefix

↓

Causal

↓

Sequential

↓

Strong

↓

Linearizable
```

As we move upward:

- Correctness increases
- Latency usually increases
- Availability often decreases
- Complexity increases

---

# Choosing a Consistency Model

Architects balance:

```text
Correctness

↓

Latency

↓

Availability

↓

Business Risk
```

---

# 15.1 Weak Consistency

Weak consistency provides almost no guarantee regarding when updates become visible.

Example:

```
Write

↓

Unknown

↓

Read
```

The reader may observe:

- New value
- Old value

Both are acceptable.

---

## Example

A weather application.

Temperature updates every few seconds.

Seeing:

```
30°C
```

instead of:

```
31°C
```

is acceptable.

---

## Advantages

- Very low latency
- High throughput
- Excellent scalability

---

## Disadvantages

- Stale reads
- No ordering guarantees
- Unsuitable for financial systems

---

# Typical Use Cases

- Analytics
- Monitoring dashboards
- Advertisement systems
- IoT telemetry
- Sensor networks

---

# 15.2 Eventual Consistency

The most common distributed consistency model.

Definition:

> **If no new writes occur, eventually every replica converges to the same value.**

Notice:

It does **not** specify:

- When
- How fast

Only that convergence eventually happens.

---

# Example

```text
Write

↓

Primary Updated

↓

Replica A Updated

↓

Replica B Updated

↓

Replica C Updated
```

During propagation:

Different users may observe different values.

Eventually:

Everyone observes the same value.

---

# Timeline Example

```
10:00:00

Price = ₹100
```

Write:

```
₹120
```

Immediately:

```text
Replica A

₹120

Replica B

₹100

Replica C

₹100
```

A few seconds later:

```text
Replica A

₹120

Replica B

₹120

Replica C

₹120
```

Consistency restored.

---

# Advantages

- Excellent scalability
- High availability
- Fast writes
- Multi-region friendly

---

# Disadvantages

- Stale reads
- Temporary inconsistency
- Conflict resolution required

---

# Typical Use Cases

- Social media
- DNS
- Product catalogs
- Recommendations
- User preferences

---

# 15.3 Read-after-Write Consistency

Guarantee:

After a client successfully writes data,

that same client immediately sees the updated value.

Other clients may still see stale data.

---

# Example

Customer updates profile picture.

Immediately refreshes profile.

Expected:

```
New Profile Picture
```

Not:

```
Old Profile Picture
```

---

# Timeline

```text
User Writes

↓

Same User Reads

↓

Latest Value
```

Other users:

May temporarily observe:

Old value.

---

# Advantages

- Better user experience
- Predictable behavior

---

# Typical Use Cases

- User profile updates
- Password changes
- Account settings
- Shopping cart

---

# 15.4 Monotonic Reads

Guarantee:

A client never observes older data after already seeing newer data.

---

# Bad Example

```
Read

Version 5

↓

Later Read

Version 3
```

Impossible under monotonic reads.

---

# Correct Behavior

```
Version 3

↓

Version 5

↓

Version 6
```

Versions only move forward.

---

# Example

Messaging application.

User reads:

```
50 Messages
```

Refreshing should never show:

```
48 Messages
```

---

# Advantages

Improves user confidence.

---

# 15.5 Monotonic Writes

Guarantee:

Writes from the same client are applied in order.

---

Example

User updates:

```
Draft

↓

Submitted

↓

Approved
```

Database should never process:

```
Approved

↓

Draft
```

Ordering matters.

---

# Use Cases

- Workflow systems
- Order processing
- Approval systems
- State machines

---

# 15.6 Session Consistency

Guarantees remain valid within one user session.

A session provides:

- Read-after-write
- Monotonic reads
- Monotonic writes

for that specific client.

---

# Example

Customer logs in.

Updates address.

Navigates multiple pages.

Every page should display:

```
New Address
```

Other users may still observe:

Old address.

---

# Session Timeline

```text
Login

↓

Update

↓

Read

↓

Read

↓

Logout
```

Within the session,

data appears consistent.

---

# Advantages

Excellent user experience.

Moderate implementation complexity.

---

# 15.7 Consistent Prefix

Guarantee:

Reads never observe writes in the wrong order.

---

Example

Messages:

```
Message A

↓

Message B

↓

Message C
```

Impossible result:

```
A

↓

C

↓

B
```

Ordering preserved.

---

# Example

Chat Application.

Users should not receive:

```
"Goodbye"

before

"Hello"
```

---

# Typical Use Cases

- Messaging
- Event Streams
- Kafka
- Activity Feeds

---

# 15.8 Causal Consistency

Guarantee:

Cause always appears before effect.

Independent events may appear in different orders.

---

# Example

Alice posts:

```
Let's meet tomorrow.
```

Bob replies:

```
Sounds good.
```

Reading:

```
Reply

↓

Original Message
```

violates causality.

---

# Causal Timeline

```text
Post

↓

Reply
```

Never:

```text
Reply

↓

Post
```

---

# Advantages

Natural user experience.

Stronger than eventual consistency.

---

# Typical Use Cases

- Social media
- Collaborative editing
- Messaging
- Comments

---

# 15.9 Sequential Consistency

Guarantee:

All operations appear to execute in one global order.

However,

that order does **not** have to match real-world time.

---

Example

Two users update:

```
A

B
```

Every client observes:

```
A

↓

B
```

or

```
B

↓

A
```

Both acceptable.

What matters:

Everyone agrees.

---

# Advantages

Simple programming model.

---

# Disadvantages

Requires coordination.

Higher latency.

---

# 15.10 Strong Consistency

Guarantee:

Every read immediately returns the latest committed write.

No stale reads.

---

Timeline

```text
Write

↓

Commit

↓

Read

↓

Latest Value
```

---

# Banking Example

Transfer completes.

Every ATM,

mobile app,

internet banking,

and branch office

must display:

```
Updated Balance
```

---

# Advantages

- Predictable
- Easy reasoning
- Financial correctness

---

# Disadvantages

- Higher latency
- Lower availability during partitions
- Expensive across regions

---

# Typical Use Cases

- Banking
- Trading
- Payments
- Inventory reservation

---

# 15.11 Linearizability

Often considered the strongest practical consistency model.

Definition:

Every operation appears to occur instantaneously at one point between:

- Request
- Response

---

Timeline

```text
Client

↓

Write

↓

Success

↓

Any Future Read

↓

Latest Value
```

No client can observe older data after a completed write.

---

# Example

Inventory:

```
1 Laptop
```

Customer purchases.

Immediately:

Every future read shows:

```
0 Laptops
```

Impossible for another customer to buy the same item.

---

# Why Linearizability Matters

It preserves the illusion of:

```
One System

↓

One Copy

↓

One Truth
```

even though data is distributed.

---

# Cost

Linearizability often requires:

- Consensus
- Majority agreement
- Network coordination

Higher correctness.

Higher latency.

---

# Comparison Table

| Model | Stale Reads | Ordering | Typical Use Cases |
|--------|-------------|----------|-------------------|
| Weak | Yes | None | Analytics |
| Eventual | Yes | None | Social Media |
| Read-after-Write | Other clients only | Partial | User Profiles |
| Monotonic Reads | No regression | Partial | Dashboards |
| Monotonic Writes | Ordered writes | Same client | Workflows |
| Session | Session guarantees | Session | Web Applications |
| Consistent Prefix | Ordered reads | Yes | Messaging |
| Causal | Cause before effect | Causal | Collaboration |
| Sequential | Global order | Yes | Shared Systems |
| Strong | No | Latest committed | Banking |
| Linearizable | No | Real-time | Financial Systems |

---

# Choosing the Right Model

| Business Requirement | Recommended Model |
|----------------------|-------------------|
| Banking | Linearizable |
| Stock Trading | Strong |
| Inventory Reservation | Strong |
| Shopping Cart | Read-after-Write |
| User Profile | Session |
| Chat Messages | Consistent Prefix |
| Social Media Feed | Eventual |
| Analytics Dashboard | Weak |

---

# Common Misconceptions

### Myth

Eventual consistency means data is permanently inconsistent.

**Reality**

Replicas eventually converge if no additional writes occur.

---

### Myth

Strong consistency is always better.

**Reality**

Strong consistency introduces higher latency and reduced availability during network partitions.

---

### Myth

Linearizability and sequential consistency are identical.

**Reality**

Sequential consistency preserves a single global order but does not require that order to reflect real-world time. Linearizability additionally respects real-time ordering.

---

# Architect's Perspective

Choosing a consistency model is **one of the most important architectural decisions** in distributed systems.

The question is never:

> **"Which consistency model is the strongest?"**

The correct question is:

> **"Which consistency guarantee minimizes business risk while meeting performance, availability, and scalability requirements?"**

Architecture is about selecting the **appropriate guarantee**, not the strongest one.

---

**End of Part 3**

The next section will cover:

- **CAP Theorem**
- **PACELC Theorem**
- **Leader-Based Replication**
- **Multi-Leader Replication**
- **Leaderless Replication**
- **Quorum Reads & Writes**
- **Consensus Algorithms (Raft & Paxos Overview)**
- **Conflict Resolution**
- **Split-Brain**
- **Consistency Decision Matrix**
