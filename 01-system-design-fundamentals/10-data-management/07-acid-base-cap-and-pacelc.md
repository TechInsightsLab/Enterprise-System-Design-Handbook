
# 61. ACID

> **ACID is a set of guarantees that ensure database transactions execute reliably, correctly, and consistently even in the presence of failures.**

Almost every enterprise transactional database implements ACID.

Examples:

- Banking
- Payments
- ERP
- Inventory
- Airline Reservation

Whenever business correctness is critical,

ACID becomes essential.

---

# Why ACID Exists

Imagine transferring money.

Account A:

```
₹10,000
```

Transfer:

```
₹5,000
```

to:

Account B.

Without transaction guarantees,

system may:

```
Debit Account A

↓

Application Crash

↓

Credit Never Happens
```

Money disappears.

Business cannot tolerate this.

---

# ACID Properties

```text
ACID

↓

Atomicity

Consistency

Isolation

Durability
```

Each property solves a different problem.

---

# 62. Atomicity

> **Atomicity guarantees that a transaction either completes entirely or not at all.**

There is no:

```
Half Transaction
```

---

# Example

Money Transfer.

```text
Debit Account

↓

Credit Account
```

Either:

Both succeed.

Or:

Neither happens.

---

# Failure Example

Without Atomicity.

```text
Debit

↓

Crash

↓

Credit Missing
```

Money lost.

---

# Correct Behavior

```text
Debit

↓

Credit

↓

Commit

-------------------

Failure

↓

Rollback
```

Everything restored.

---

# Everyday Analogy

ATM Withdrawal.

Either:

- Cash dispensed
- Balance updated

or

Neither.

Never one without the other.

---

# 63. Consistency (Database Perspective)

> **Consistency guarantees that every committed transaction moves the database from one valid state to another valid state while preserving defined rules and constraints.**

> **Important:** This is **database consistency**, which is different from the distributed systems consistency discussed in **Chapter 7**.

---

# Example

Business Rule.

```
Balance

Cannot Be Negative
```

Transaction attempts:

```
₹100

↓

Withdraw ₹500
```

Database rejects.

Rule preserved.

---

# Constraint Examples

- Primary Key
- Foreign Key
- Unique Constraint
- Check Constraint
- Business Rules

All remain valid after commit.

---

# Example

Before:

```
Inventory

5
```

After purchase:

```
4
```

Valid.

Not:

```
-10
```

---

# 64. Isolation

> **Isolation ensures that concurrently executing transactions behave as though they execute independently.**

Users should not observe:

Half-finished work.

---

# Example

Transaction A.

Updates:

```
Balance
```

Not committed.

Transaction B.

Reads.

Should not see:

Temporary state.

---

# Without Isolation

```text
Transaction A

Update

↓

Transaction B

Reads

↓

Rollback
```

Transaction B observed invalid data.

---

# With Isolation

Transaction B waits,

or reads a consistent snapshot,

depending on the isolation level.

---

# Isolation Levels

Previously discussed:

- Read Uncommitted
- Read Committed
- Repeatable Read
- Serializable

Higher isolation generally increases correctness but may reduce concurrency.

---

# 65. Durability

> **Durability guarantees that once a transaction commits, the data survives system failures.**

Even if:

- Server crashes
- Power fails
- JVM terminates

Committed data remains.

---

# Example

Customer completes payment.

Database:

```
Commit
```

Immediately afterward:

Power outage.

After restart:

Payment still exists.

---

# How Durability Works

Databases typically use:

- Transaction Logs
- Write-Ahead Logging (WAL)
- Checkpoints
- Replication
- Persistent Storage

---

# Write-Ahead Logging (WAL)

Simplified workflow.

```text
Write Transaction Log

↓

Flush Log

↓

Modify Database Pages

↓

Commit
```

The log becomes the recovery source.

---

# ACID Summary

| Property | Meaning |
|-----------|---------|
| Atomicity | All or Nothing |
| Consistency | Valid State Preserved |
| Isolation | Concurrent Transactions Behave Correctly |
| Durability | Committed Data Survives Failures |

---

# ACID Example

Money Transfer.

```text
Begin Transaction

↓

Debit

↓

Credit

↓

Commit

↓

Durable
```

Failure at any intermediate step:

Rollback.

---

# 66. BASE

As distributed systems became larger,

strict ACID guarantees became expensive.

Many NoSQL systems adopted:

```
BASE
```

---

# Meaning

```text
Basically Available

↓

Soft State

↓

Eventually Consistent
```

BASE prioritizes:

Availability.

Scalability.

Fault tolerance.

---

# 67. Basically Available

System continues responding,

even during failures.

Some responses may be:

- Stale
- Partial
- Delayed

Availability prioritized.

---

# Example

Social Media.

Follower count:

```
1000
```

Another server shows:

```
999
```

Brief inconsistency acceptable.

Application remains available.

---

# 68. Soft State

State may change over time,

even without new client requests,

because replicas continue synchronizing.

---

# Example

Replication.

Node A:

```
100
```

Node B:

```
98
```

After synchronization:

Both become:

```
100
```

State evolves.

---

# 69. Eventually Consistent

> **If no new updates occur, all replicas eventually converge to the same value.**

Not immediate.

Eventually.

---

# Example

Profile update.

Mumbai:

```
New Profile Photo
```

Frankfurt:

Still old photo.

After replication:

All regions show:

New photo.

---

# Where Eventual Consistency Works Well

Examples:

- Social media feeds
- Product catalogs
- User preferences
- Comments
- Reviews
- Analytics

Minor temporary inconsistency acceptable.

---

# Where Eventual Consistency Is Dangerous

Avoid for:

- Banking balances
- Stock trading
- Payment settlements
- Medical records
- Inventory deduction (without additional safeguards)

Business correctness outweighs availability.

---

# ACID vs BASE

| ACID | BASE |
|-------|------|
| Strong Consistency | Eventual Consistency |
| Transaction-Oriented | Availability-Oriented |
| Traditional RDBMS | Many Distributed NoSQL Systems |
| Correctness First | Scalability & Availability First |

---

# 70. CAP Theorem

> **CAP Theorem states that during a network partition, a distributed system can guarantee at most two of the following three properties:**

- Consistency (C)
- Availability (A)
- Partition Tolerance (P)

---

# Why CAP Exists

Imagine:

Three database nodes.

```text
Node A

↓

Node B

↓

Node C
```

Suddenly,

network cable breaks.

```text
Node A

X

Node B

↓

Node C
```

Partition.

Now:

What should the system do?

---

# CAP Components

## Consistency

Every client sees:

Latest successful write.

No stale reads.

---

## Availability

Every request receives a response.

Not necessarily the latest data.

---

## Partition Tolerance

System continues operating despite communication failures between nodes.

---

# Important Clarification

Partition tolerance is **not optional** in distributed systems.

Real networks fail.

Therefore,

when a partition occurs,

architects usually choose between:

```
Consistency

or

Availability
```

---

# CA Systems

```text
Consistency

+

Availability
```

Possible only when:

No partition exists.

Traditional single-node databases approximate CA because network partitions are not part of the architecture.

---

# CP Systems

Choose:

Consistency.

Reject or delay some requests during a partition.

Examples include systems that prioritize correctness over availability for partitioned data.

---

# AP Systems

Choose:

Availability.

Continue serving requests.

Replicas synchronize later.

Common in systems where temporary inconsistency is acceptable.

---

# CAP Visualization

```text
            CAP

          /  |  \

         C   A   P

During Partition:

Choose

CP

or

AP
```

---

# Banking Example

Transfer money.

Network partition.

Would you prefer:

Wrong balance?

Or:

Temporary unavailability?

Bank chooses:

Consistency.

---

# Social Media Example

Follower count.

Temporary difference:

```
1000

vs

999
```

Acceptable.

Availability preferred.

---

# CAP Misconceptions

### Myth

Every database is CP or AP all the time.

**Reality**

CAP only applies **when a network partition occurs**.

When there is no partition,

many systems can provide both consistency and availability.

---

### Myth

Partition tolerance can be disabled.

**Reality**

Distributed systems cannot prevent network failures.

They must tolerate them.

---

# 71. PACELC Theorem

CAP explains trade-offs **during partitions**.

But what about normal operation?

PACELC extends CAP.

---

# Meaning

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

---

# Interpretation

During normal operation,

many distributed databases trade:

Latency

against

Consistency.

---

# Example

Suppose.

Primary:

Mumbai.

Replica:

Frankfurt.

Write request.

Should system:

Wait for Frankfurt?

↓

Higher latency.

Higher consistency.

---

Or:

Respond immediately?

↓

Lower latency.

Temporary inconsistency.

---

# PACELC Diagram

```text
Partition?

↓

YES

↓

A or C

--------------------

NO

↓

L or C
```

---

# Why PACELC Matters

Modern cloud databases operate across:

- Regions
- Countries
- Continents

Even without failures,

network distance creates latency.

Architects must decide whether to optimize for:

- Faster responses
- Stronger consistency

---

# CAP vs PACELC

| CAP | PACELC |
|------|---------|
| During Partitions | During Partitions + Normal Operation |
| C vs A | C vs A, L vs C |
| Simpler | More Realistic |

---

# Common Mistakes

### Assuming ACID Solves Distributed Problems

ACID applies primarily within a database transaction.

Distributed systems introduce additional coordination challenges.

---

### Using Eventual Consistency Everywhere

Business-critical data may require stronger guarantees.

---

### Ignoring Latency

Global consistency often increases response times.

Measure the business impact before choosing stronger consistency.

---

### Treating CAP as a Database Feature List

CAP is a theorem about trade-offs,

not a marketing label.

---

# Architect's Perspective

Every enterprise architect must understand that **correctness has a cost**.

That cost may be:

- Latency
- Reduced availability
- Operational complexity
- Lower throughput

There is no universally correct choice.

Instead,

choose based on business requirements.

Examples:

| Business Domain | Typical Priority |
|-----------------|------------------|
| Banking | Strong Consistency |
| Payments | Strong Consistency |
| Inventory | Strong Consistency or Carefully Managed Eventual Consistency |
| Social Media | Availability |
| Product Catalog | Availability |
| Analytics | Availability |

The guiding principle is:

> **Design for the business, not for the theorem.**

---

**End of Part 7**

The next part will cover:

- **Consistency Models**
- **Strong Consistency**
- **Eventual Consistency**
- **Causal Consistency**
- **Read-Your-Writes**
- **Monotonic Reads**
- **Session Consistency**
- **Quorum Reads/Writes**
- **Vector Clocks**
- **Conflict Resolution**
