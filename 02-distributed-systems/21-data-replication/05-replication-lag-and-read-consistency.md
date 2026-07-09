
# 6. Replication Lag & Read Consistency

One of the biggest challenges.

In distributed systems.

Is not.

Replication itself.

It is.

**Replication Lag.**

Almost every.

Distributed database.

Must deal with it.

---

# What is Replication Lag?

> **Replication Lag is the delay between when a write is committed on the Primary and when that write becomes visible on one or more replicas.**

This delay.

May be.

- Milliseconds
- Seconds
- Minutes

In extreme cases.

Even hours.

---

# Simple Example

Suppose.

Customer updates.

Their address.

```text
Primary

↓

Address = Pune

↓

Replica

↓

Still Old Address
```

The update.

Has not yet.

Reached.

The replica.

---

# Timeline

```text
Time

↓

10:00:00

Write

↓

Primary Updated

↓

10:00:00.200

Replication Starts

↓

10:00:00.500

Replica Updated
```

Replication Lag.

```text
500 ms
```

---

# Why Does Replication Lag Exist?

Replication.

Requires.

Time.

Because.

Data must travel.

Across.

Networks.

---

# Typical Causes

Replication lag.

Occurs because of.

- Network latency
- Large transactions
- Slow disks
- High write traffic
- CPU bottlenecks
- Replica overload
- Cross-region communication
- Bandwidth limitations

---

# Example

Primary.

Processes.

```text
10,000 Writes/sec
```

Replica.

Can process.

```text
7,000 Writes/sec
```

Question.

What happens?

A backlog.

Begins to grow.

Replication lag.

Increases.

---

# Geographic Example

Suppose.

Primary.

Runs.

In Mumbai.

Replica.

Runs.

In New York.

Every update.

Must cross.

Thousands.

Of kilometers.

Even.

At the speed of light.

There is.

Physical delay.

---

# Business Example

Customer.

Places an order.

Immediately.

Refreshes.

Order History.

Application.

Reads.

From a replica.

Replica.

Has not yet.

Received.

The order.

Customer sees.

```text
No Orders Found
```

The customer thinks.

The order failed.

But.

The order exists.

Only.

The replica.

Is behind.

---

# Visual Example

```text
Customer

↓

Place Order

↓

Primary Updated

↓

Success

↓

Customer Refreshes

↓

Replica

↓

Old Data

↓

Confusion
```

---

# Why Replication Lag Matters

Replication lag.

Can cause.

- Stale reads
- Incorrect dashboards
- Duplicate purchases
- User confusion
- Failed business workflows
- Cache inconsistency

---

# Measuring Replication Lag

Most databases.

Expose metrics.

Like.

```text
Replication Delay

=

Current Time

-

Last Replicated Transaction
```

---

# Example

```text
Current Time

10:00:05

Last Replica Update

10:00:02

↓

Replication Lag

3 Seconds
```

---

# Production Monitoring

Common metrics.

Include.

- Replica Delay
- Replication Queue Size
- Replication Throughput
- WAL Lag
- Binlog Position
- Apply Delay

These metrics.

Should always.

Be monitored.

---

# Read Consistency Problems

Replication lag.

Creates.

Read consistency issues.

---

Example.

Customer.

Updates profile.

Immediately.

Reads profile.

Question.

Should.

The updated profile.

Be visible?

Depends.

On.

Which replica.

Serves the read.

---

# Different Read Guarantees

Distributed systems.

Offer.

Different guarantees.

```text
Read Consistency

│

├── Eventual Consistency

├── Read-after-Write

├── Monotonic Reads

├── Consistent Prefix

└── Session Consistency
```

Let's study.

Each one.

---

# 6.1 Eventual Consistency

> **If no new updates occur, all replicas will eventually converge to the same value.**

Notice.

The keyword.

Eventually.

No guarantee.

Of.

Immediate consistency.

---

# Example

Customer changes.

Email.

```text
Primary

↓

abc@gmail.com
```

Replica.

Still shows.

```text
old@gmail.com
```

Five seconds later.

```text
abc@gmail.com
```

Eventually.

Every replica.

Agrees.

---

# Timeline

```text
Write

↓

Primary Updated

↓

Lag

↓

Replica Updated

↓

Consistent
```

---

# Business Examples

Eventual consistency.

Works well.

For.

- Social Media
- Product Catalog
- News Feed
- Analytics
- Search

Users.

Can tolerate.

Small delays.

---

# Advantages

- High availability
- Excellent scalability
- Low latency
- Better throughput

---

# Disadvantages

- Stale reads
- Temporary inconsistency
- User confusion

---

# Architect's Perspective

Eventual consistency.

Does not mean.

Incorrect forever.

It means.

Correct.

Eventually.

---

# 6.2 Read-after-Write Consistency

> **After a client successfully writes data, every subsequent read by that same client returns the updated value.**

This guarantee.

Eliminates.

One of the most.

Common user frustrations.

---

# Example

Customer.

Changes password.

Immediately.

Views profile.

Expected.

```text
New Password

Information Visible
```

Not.

Old information.

---

# Without Read-after-Write

```text
Write

↓

Primary

↓

Success

↓

Read

↓

Replica

↓

Old Data
```

Very frustrating.

---

# With Read-after-Write

```text
Write

↓

Primary

↓

Success

↓

Next Read

↓

Primary

↓

Updated Data
```

Correct.

---

# Implementation Techniques

Several techniques.

Can provide.

Read-after-Write.

---

## Technique 1

Read From Primary

Immediately after writes.

---

```text
Write

↓

Primary

↓

Next Read

↓

Primary
```

Simple.

Reliable.

---

## Technique 2

Sticky Sessions

Keep.

The customer.

Connected.

To the same server.

During.

The session.

---

```text
User

↓

Server A

↓

Primary
```

Future reads.

Remain.

On the same path.

---

## Technique 3

Wait For Replication

Application.

Waits.

Until.

Replicas catch up.

Before.

Allowing reads.

---

# Business Examples

Read-after-write.

Is critical for.

- User Profile Updates
- Password Changes
- Shopping Cart
- Banking
- Settings
- Order Confirmation

---

# 6.3 Monotonic Reads

> **Once a client has seen a particular version of data, future reads will never return an older version.**

---

# Example

Suppose.

Customer reads.

```text
Balance

₹12,000
```

Next request.

Should never show.

```text
₹10,000
```

That would appear.

As if.

Time moved backwards.

---

# Timeline

Correct.

```text
₹10K

↓

₹12K

↓

₹13K
```

Incorrect.

```text
₹10K

↓

₹12K

↓

₹10K

↓

₹13K
```

---

# Why It Happens

Suppose.

First read.

Uses.

Replica A.

Second read.

Uses.

Replica B.

Replica B.

Has not yet.

Received.

Latest updates.

Customer.

Sees.

Older data.

---

# Business Example

Order Status.

```text
Packed

↓

Shipped

↓

Delivered
```

Customer.

Should never see.

```text
Delivered

↓

Packed
```

Impossible.

---

# Implementation

Monotonic reads.

Usually require.

- Session affinity
- Replica version tracking
- Client routing
- Vector clocks
- Session tokens

---

# 6.4 Consistent Prefix Reads

> **If a sequence of writes has a causal relationship, readers should observe those writes in the same order.**

This prevents.

Observing.

Events.

Out of sequence.

---

# Example

Workflow.

```text
Create Order

↓

Payment

↓

Shipping
```

Customer.

Must never observe.

```text
Shipping

↓

Create Order
```

---

# Business Example

Chat.

```text
Message 1

↓

Message 2

↓

Message 3
```

Readers.

Should never receive.

```text
Message 3

↓

Message 1

↓

Message 2
```

---

# Why It Happens

Different replicas.

May receive.

Updates.

At different times.

Ordering.

May temporarily differ.

---

# 6.5 Session Consistency

> **Session Consistency guarantees that all operations within a user's session observe a consistent view of the data.**

This combines.

Several guarantees.

Such as.

- Read-after-write
- Monotonic reads
- Consistent session behavior

---

# Example

Customer logs in.

Updates profile.

Views profile.

Updates address.

Views address.

Everything.

Appears.

Consistent.

Throughout.

The session.

---

# How Session Consistency Works

Usually.

Using.

- Sticky Sessions
- Session Tokens
- Session IDs
- Version Tracking

---

# Example

```text
Customer

↓

Session Token

↓

Replica Selection

↓

Consistent Reads
```

---

# Read Consistency Comparison

| Consistency Model | Guarantee |
|-------------------|-----------|
| Eventual | Data converges eventually |
| Read-after-Write | User immediately sees own writes |
| Monotonic Reads | Never read older versions |
| Consistent Prefix | Observe causally related writes in order |
| Session Consistency | Consistent experience within a session |

---

# Choosing the Right Read Consistency

| Business Requirement | Recommended Model |
|----------------------|-------------------|
| Social Media Feed | Eventual |
| Product Catalog | Eventual |
| Password Change | Read-after-Write |
| Shopping Cart | Read-after-Write |
| Banking Dashboard | Monotonic Reads |
| Chat Messages | Consistent Prefix |
| User Portal | Session Consistency |

---

# Common Misconceptions

### Misconception 1

> Replication lag means replication is broken.

False.

Small replication lag.

Is expected.

Even in healthy systems.

---

### Misconception 2

> Eventual consistency means random consistency.

False.

Replicas.

Eventually converge.

If no further writes occur.

---

### Misconception 3

> Read-after-write requires synchronous replication.

False.

Applications can implement.

Read-after-write.

Using routing.

Sticky sessions.

Or reading from the Primary.

---

### Misconception 4

> Every application needs strong consistency.

False.

Many workloads.

Work perfectly.

With eventual consistency.

And gain much better scalability.

---

# Architect's Decision Framework

Before choosing a read consistency model, ask:

| Question | Why It Matters |
|----------|----------------|
| Can users tolerate stale data? | Determines if eventual consistency is acceptable |
| Should users immediately see their own updates? | Suggests read-after-write consistency |
| Must data always move forward? | Indicates monotonic reads |
| Does event order matter? | Requires consistent prefix reads |
| Is a consistent user session important? | Suggests session consistency |

---

# Architect's Perspective

Replication lag is not a bug.

It is an inevitable consequence of distributed systems.

The role of an architect is not to eliminate lag entirely—that is often impossible.

Instead, architects choose **consistency guarantees** that match the business experience:

- Product catalogs can tolerate stale data.
- Password changes cannot.
- Chat messages require ordering.
- Banking requires strong guarantees.

Understanding these trade-offs allows you to design systems that are both scalable and correct.

The next section explores **Quorum Replication (N, R, W)**, one of the most important techniques used by leaderless databases such as Cassandra and Dynamo to balance consistency, availability, and latency.

---

**End of Part 5**

The next part will cover:

- **Quorum Replication**
- **Replication Factor (N)**
- **Read Quorum (R)**
- **Write Quorum (W)**
- **The Rule: R + W > N**
- **Read Repair**
- **Hinted Handoff**
- **Anti-Entropy**
