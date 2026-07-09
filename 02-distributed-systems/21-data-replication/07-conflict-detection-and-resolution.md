
# 8. Conflict Detection & Conflict Resolution

> **Conflict Resolution is the process of detecting and resolving different versions of the same data that were created independently on different replicas.**

Conflict resolution.

Is one of.

The hardest problems.

In distributed systems.

It becomes especially important.

In.

- Multi-Leader Replication
- Leaderless Replication
- Offline Applications
- Global Databases

---

# Why Do Conflicts Occur?

Suppose.

Customer A.

And.

Customer B.

Update.

The same customer record.

At the same time.

---

Customer A.

```text
Phone

1111111111
```

---

Customer B.

```text
Phone

9999999999
```

---

Question.

Which one.

Is correct?

Both updates.

Succeeded.

Both users.

Believe.

Their update.

Was accepted.

---

# Conflict Timeline

```text
          India Leader

Phone = 1111111111

               │

               │

               ▼

        Synchronization

               ▲

               │

               │

       Germany Leader

Phone = 9999999999
```

When synchronization occurs.

The database discovers.

Two different versions.

---

# What is a Conflict?

> **A conflict occurs when two or more replicas independently modify the same data before synchronizing with each other.**

The replicas.

Have diverged.

Now.

The system.

Must converge.

Back.

To one version.

---

# Conflict Detection

Before resolving.

A conflict.

The system.

Must detect it.

Common techniques include.

```text
Conflict Detection

│

├── Timestamps

├── Version Numbers

├── Vector Clocks

├── Version Vectors

├── Checksums

└── Application Logic
```

---

# Strategy 1 — Timestamp Comparison

The simplest method.

Uses timestamps.

---

Example.

```text
Version A

10:00:01

------------------

Version B

10:00:03
```

Database decides.

```text
Newest Wins
```

---

# Advantages

- Very simple
- Fast
- Easy implementation

---

# Problems

Physical clocks.

May disagree.

Clock skew.

Can produce.

Incorrect winners.

---

# Example

Server A.

Clock.

5 seconds ahead.

Server B.

Correct time.

The older update.

May appear.

Newer.

Incorrectly.

---

# Strategy 2 — Version Numbers

Every update.

Increments.

A version number.

---

Example.

```text
Version 5

↓

Version 6

↓

Version 7
```

Higher version.

Usually wins.

---

# Advantages

- Simple
- Predictable
- No dependency on physical clocks

---

# Problems

Works well.

Only.

When updates.

Occur sequentially.

Not.

Concurrent writes.

---

# Strategy 3 — Vector Clocks

As learned.

In Chapter 19.

Vector clocks.

Detect.

Concurrent updates.

---

Example.

Replica A.

```text
[4,2]
```

Replica B.

```text
[2,5]
```

Neither vector.

Dominates.

The other.

Conflict detected.

---

# Why Vector Clocks Are Powerful

Instead of guessing.

Which update.

Is newer.

Vector clocks.

Determine.

Whether.

One update.

Actually depends.

On another.

---

# Example

```text
[5,3]

↓

[6,3]
```

No conflict.

---

Example.

```text
[5,2]

↓

[3,6]
```

Concurrent.

Conflict exists.

---

# Strategy 4 — Checksums

Large databases.

May compare.

Hashes.

Instead of.

Entire records.

---

Example.

```text
Replica A

SHA256

ABCD

------------------

Replica B

SHA256

XYZ1
```

Different checksum.

Means.

Data differs.

---

# Conflict Resolution Strategies

Once detected.

The system.

Must resolve.

The conflict.

---

Major strategies.

```text
Resolution

│

├── Last Write Wins

├── Manual Resolution

├── Merge

├── CRDT

├── Operational Transformation

└── Application Rules
```

---

# 8.1 Last Write Wins (LWW)

> **The update with the newest timestamp replaces all previous versions.**

---

Example.

```text
10:00:01

↓

Phone = 1111

--------------------

10:00:05

↓

Phone = 9999
```

Winner.

```text
9999
```

---

# Advantages

- Very simple
- Automatic
- Fast

---

# Disadvantages

A perfectly valid.

Earlier update.

May disappear.

Forever.

This is called.

**Lost Update.**

---

# Example

Customer A.

Updates.

Address.

Customer B.

Updates.

Phone.

Same timestamp.

LWW.

May discard.

One update.

Even though.

Both changes.

Were valid.

---

# Typical Systems

LWW.

Is common in.

- Cassandra (optional)
- Dynamo-style systems
- Redis (certain scenarios)
- Mobile synchronization

---

# 8.2 Manual Resolution

Sometimes.

The application.

Cannot decide.

Automatically.

---

Example.

Git.

Branch A.

```text
Hello World
```

Branch B.

```text
Hello Everyone
```

Git.

Stops.

Developer decides.

---

Enterprise Example.

Insurance.

Two employees.

Update.

The same claim.

Human review.

Determines.

Correct version.

---

# Advantages

- Highest correctness
- Business-aware
- No accidental data loss

---

# Disadvantages

- Slow
- Requires human intervention
- Poor scalability

---

# 8.3 Merge Strategy

Instead of.

Choosing.

One winner.

Merge.

Both updates.

---

Example.

Customer A.

Updates.

```text
Phone
```

Customer B.

Updates.

```text
Address
```

No overlap.

Database merges.

Both fields.

---

Result.

```text
Phone

Updated

+

Address

Updated
```

No data lost.

---

# Example

Shopping Cart.

Customer.

Adds.

```text
Laptop
```

Offline.

Another device.

Adds.

```text
Mouse
```

Merge.

Results.

```text
Laptop

Mouse
```

Not.

Either-or.

---

# Advantages

- Preserves data
- Better user experience
- Automatic

---

# Disadvantages

Not every.

Business object.

Can be merged.

---

# 8.4 Operational Transformation (OT)

Operational Transformation.

Is used.

For.

Real-time collaboration.

---

Example.

Google Docs.

Two users.

Edit.

The same paragraph.

---

User A.

Inserts.

```text
Hello
```

---

User B.

Deletes.

Another word.

---

Instead of.

Replacing.

Entire documents.

OT.

Transforms.

Operations.

Into.

Compatible changes.

---

# Business Examples

Operational Transformation.

Powers.

- Google Docs
- Microsoft Office Online
- Collaborative editors

---

# Advantages

- Excellent collaboration
- Near real-time editing
- High concurrency

---

# Disadvantages

- Very complex
- Difficult implementation
- Specialized algorithms

---

# 8.5 CRDT (Conflict-Free Replicated Data Types)

> **CRDTs are data structures designed so that replicas automatically converge to the same state without requiring manual conflict resolution.**

CRDTs.

Guarantee.

Eventual convergence.

Regardless.

Of message ordering.

---

# Example

Distributed Counter.

Replica A.

```text
+2
```

Replica B.

```text
+3
```

Merged result.

```text
+5
```

No conflict.

---

# Example

Distributed Set.

Replica A.

Adds.

```text
Laptop
```

Replica B.

Adds.

```text
Phone
```

Merge.

```text
Laptop

Phone
```

---

# CRDT Types

Examples include.

- G-Counter
- PN-Counter
- OR-Set
- LWW-Register
- Grow-only Set
- Map CRDT

---

# Typical Systems

CRDTs.

Appear in.

- Redis CRDT
- Riak
- Akka Distributed Data
- Edge Computing
- Offline-first Apps

---

# Conflict Resolution Comparison

| Strategy | Automatic | Data Loss Risk | Complexity | Typical Use |
|----------|-----------|----------------|------------|-------------|
| Last Write Wins | ✅ | High | Low | Simple key-value data |
| Version Numbers | ✅ | Medium | Low | Sequential updates |
| Manual Resolution | ❌ | None | High | Business workflows |
| Merge | ✅ | Low | Medium | Independent fields |
| Operational Transformation | ✅ | Very Low | Very High | Collaborative editing |
| CRDT | ✅ | Very Low | High | Distributed counters, sets |

---

# Business Examples

## Banking

Conflict.

Not acceptable.

Use.

Manual review.

Or.

Strong consistency.

---

## Shopping Cart

Merge.

Usually.

Best.

---

## Google Docs

Operational Transformation.

Or.

CRDT.

---

## Product Likes

CRDT Counter.

Excellent fit.

---

## Inventory

Strong consistency.

Avoid conflicts.

Rather than.

Resolving them later.

---

# Production Considerations

When conflicts occur.

Architects should ask.

- Can conflicts be prevented?
- Can they be detected automatically?
- Can they be merged safely?
- Must humans decide?
- Is data loss acceptable?
- How often do conflicts occur?

Preventing conflicts.

Is usually cheaper.

Than resolving them.

---

# Common Misconceptions

### Misconception 1

> Last Write Wins always chooses the correct value.

False.

It chooses.

The newest timestamp.

Not necessarily.

The correct business value.

---

### Misconception 2

> Vector Clocks resolve conflicts.

False.

Vector clocks.

Detect.

Concurrent updates.

They do not decide.

The winner.

---

### Misconception 3

> Merge always works.

False.

Some business objects.

Cannot be merged.

Example.

Bank balances.

---

### Misconception 4

> CRDTs eliminate all distributed system problems.

False.

CRDTs solve.

Specific data synchronization problems.

They are not.

A universal solution.

---

# Architect's Decision Framework

Before selecting a conflict resolution strategy, ask:

| Question | Why It Matters |
|----------|----------------|
| Can concurrent updates occur? | Determines if conflict handling is needed |
| Is data loss acceptable? | Rules out LWW for critical data |
| Can updates be merged safely? | Suggests merge or CRDT |
| Does the business require human approval? | Suggests manual resolution |
| Is real-time collaboration required? | Suggests OT or CRDT |
| Can conflicts be prevented with stronger consistency? | May avoid the problem entirely |

---

# Architect's Perspective

Conflict resolution is fundamentally a **business problem**, not just a technical one.

Technology can detect conflicts.

Technology can merge some conflicts.

Technology can automate many conflicts.

But only the business can define:

> **Which version represents the correct business outcome?**

The best distributed systems avoid unnecessary conflicts through good data modeling and consistency guarantees, and use sophisticated resolution strategies only when concurrent updates are an unavoidable part of the business.

The next section explores **Replication Topologies, Failover, Failback, Split Brain, and Disaster Recovery**, showing how replicated systems survive real production failures.

---

**End of Part 7**

The next part will cover:

- **Failover**
- **Failback**
- **Split Brain**
- **Replication Topologies**
- **Disaster Recovery**
- **Production Architectures**
- **Real-world Database Implementations**
