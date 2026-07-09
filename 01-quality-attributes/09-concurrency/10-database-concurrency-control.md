
# 54. Database Concurrency Control

> **Concurrency is not only a programming problem—it is also a database problem.**

Modern databases serve:

- Thousands of concurrent users
- Millions of transactions
- Hundreds of updates per second

Without proper concurrency control,

databases would produce:

- Incorrect balances
- Duplicate bookings
- Lost updates
- Corrupted business data

Database Concurrency Control ensures that:

> **Multiple transactions execute concurrently while preserving data correctness.**

---

# Why Database Concurrency Control Exists

Imagine an airline booking system.

One seat remains.

Two customers simultaneously execute:

```sql
SELECT available_seats
FROM flights
WHERE flight_id = 101;
```

Both read:

```
1
```

Both execute:

```sql
UPDATE flights
SET available_seats = 0;
```

Database now confirms:

```
2 Tickets

↓

1 Seat
```

Business correctness violated.

---

# Another Example

Bank Balance:

```
₹10,000
```

Transaction A:

Withdraw:

```
₹6,000
```

Transaction B:

Withdraw:

```
₹7,000
```

Without concurrency control,

both transactions succeed.

Final balance becomes:

```
-₹3,000
```

Impossible state.

---

# Database Concurrency Goals

A database must provide:

- Correctness
- Isolation
- High Throughput
- Fairness
- Low Latency
- Recoverability

while serving many users simultaneously.

---

# Common Techniques

Modern databases use:

```text
Locks

↓

MVCC

↓

Isolation Levels

↓

Timestamp Ordering

↓

Optimistic Concurrency
```

Different databases combine multiple techniques.

---

# 55. Pessimistic Locking

> **Pessimistic Locking assumes conflicts are likely and prevents them by locking data before modification.**

Philosophy:

> "Someone else will probably modify this data."

Therefore:

Lock first.

Modify later.

---

# Workflow

```text
Acquire Lock

↓

Read Data

↓

Modify

↓

Commit

↓

Release Lock
```

No other transaction modifies the same row.

---

# Example

Inventory:

```
Laptop

Stock = 5
```

Transaction A:

```sql
SELECT *

FOR UPDATE
```

Database locks row.

Transaction B:

Attempts update.

Must wait.

---

# Timeline

```text
Transaction A

Lock

↓

Update

↓

Commit

---------------------

Transaction B

Waiting

↓

Lock Released

↓

Continue
```

---

# Advantages

- Simple mental model
- Prevents lost updates
- Strong consistency

---

# Disadvantages

- Blocking
- Deadlocks
- Lower throughput
- Poor scalability

---

# Best Use Cases

Good for:

- Banking
- Financial transfers
- Inventory deduction
- Ticket booking

Where correctness outweighs throughput.

---

# 56. Optimistic Locking

> **Optimistic Locking assumes conflicts are rare and checks for conflicts only when committing changes.**

Instead of locking,

transactions proceed normally.

Conflict detection happens later.

---

# Philosophy

> "Conflicts rarely happen."

Therefore:

Do not lock.

Verify before commit.

---

# Version Number

Common implementation:

```text
Order

Version = 5
```

Transaction reads:

```
Version 5
```

Updates row only if:

```
Version

Still

5
```

Otherwise:

Retry.

---

# Example

Initial State

```text
Balance

₹10,000

Version = 8
```

Transaction A

Reads:

```
Version 8
```

Transaction B

Also reads:

```
Version 8
```

---

Transaction A commits.

Version becomes:

```
9
```

Transaction B tries:

```
WHERE version = 8
```

Update affects:

```
0 Rows
```

Conflict detected.

Retry.

---

# SQL Example

```sql
UPDATE accounts

SET balance = ?, version = version + 1

WHERE

id = ?

AND version = 5;
```

Affected rows:

```
1

↓

Success

----------------

0

↓

Conflict
```

---

# Advantages

- No blocking
- Better scalability
- Higher throughput
- Excellent for read-heavy systems

---

# Disadvantages

- Retries
- Conflict handling
- Additional application logic

---

# Best Use Cases

- Product Catalog
- User Profiles
- CMS
- E-commerce
- Configuration

Where write conflicts are infrequent.

---

# Optimistic vs Pessimistic

| Optimistic | Pessimistic |
|-------------|-------------|
| No Lock | Lock First |
| Retry | Wait |
| Better Scalability | Better Consistency |
| Good Low Contention | Good High Contention |

---

# Choosing Between Them

Ask:

```
How often do users modify the same data?
```

Rare:

Optimistic.

Frequent:

Pessimistic.

---

# 57. MVCC (Multi-Version Concurrency Control)

> **MVCC allows readers and writers to proceed concurrently by maintaining multiple versions of data.**

This is one of the most important innovations in modern databases.

Used by:

- PostgreSQL
- MySQL InnoDB
- Oracle
- SQL Server (Snapshot Isolation)
- CockroachDB

---

# Traditional Locking

Reader:

Waits.

Writer:

Waits.

Poor concurrency.

---

# MVCC Idea

Instead of modifying a row directly,

database creates:

New Version.

Readers continue using:

Old Version.

---

# Example

Customer Record

Version 1

```
Name

John
```

Transaction updates:

```
John Smith
```

Database stores:

```text
Version 1

John

-------------------

Version 2

John Smith
```

Readers choose appropriate version.

---

# Timeline

```text
Time →

Version 1

████████

----------------

Version 2

      ████████
```

Transactions observe different snapshots.

---

# Advantages

- Readers never block writers
- Writers rarely block readers
- High concurrency
- Excellent scalability

---

# Trade-offs

- More storage
- Cleanup process required
- Version management

---

# Vacuum

PostgreSQL periodically removes:

Old row versions.

Called:

```
VACUUM
```

---

# 58. Lost Update Problem

One of the most common concurrency bugs.

---

# Example

Inventory:

```
10
```

Transaction A

Reads:

```
10
```

Transaction B

Reads:

```
10
```

---

Transaction A

Writes:

```
9
```

Transaction B

Also writes:

```
9
```

Correct answer:

```
8
```

One update lost.

---

# Timeline

```text
T1

Read 10

----------------

T2

Read 10

----------------

T1

Write 9

----------------

T2

Write 9
```

---

# Solutions

- Row Locks
- Optimistic Locking
- Atomic Update
- MVCC (depending on isolation level and conflict detection)

---

# 59. Dirty Read

> **A Dirty Read occurs when one transaction reads uncommitted data written by another transaction.**

---

# Example

Transaction A

Updates balance:

```
₹5000
```

Not committed.

Transaction B reads:

```
₹5000
```

Later:

Transaction A rolls back.

Actual balance:

```
₹10,000
```

Transaction B observed data that never truly existed.

---

# Timeline

```text
Transaction A

Update

↓

Not Commit

↓

Rollback

-------------------

Transaction B

Read

(Uncommitted Data)
```

---

# Prevention

Isolation Level:

```
READ COMMITTED

or Higher
```

---

# 60. Non-Repeatable Read

Transaction reads:

```
Balance

₹10,000
```

Later,

same transaction reads again.

Gets:

```
₹9,000
```

Another committed transaction changed the row between reads.

---

# Example

```text
T1

Read ₹10,000

↓

T2

Update ₹9,000

↓

Commit

↓

T1

Read Again

↓

₹9,000
```

Same query.

Different result.

---

# Prevention

Isolation:

```
REPEATABLE READ

or Higher
```

---

# 61. Phantom Read

More subtle.

Query:

```sql
SELECT *

FROM orders

WHERE amount > 1000;
```

Initially:

```
10 Rows
```

Another transaction inserts:

New Order.

Same transaction repeats query.

Now:

```
11 Rows
```

New row appears like:

A phantom.

---

# Timeline

```text
T1

Read

10 Orders

----------------

T2

Insert New Order

Commit

----------------

T1

Read Again

11 Orders
```

---

# Prevention

```
SERIALIZABLE
```

or equivalent mechanisms depending on the database.

---

# Comparison

| Problem | Description |
|-----------|-------------|
| Lost Update | One update overwritten |
| Dirty Read | Read uncommitted data |
| Non-Repeatable Read | Same row changes |
| Phantom Read | New rows appear |

---

# 62. Database Isolation Levels

Isolation defines:

How much concurrent transactions can interfere.

---

# Read Uncommitted

Allows:

- Dirty Reads
- Non-repeatable Reads
- Phantom Reads

Fast.

Rarely used.

---

# Read Committed

Prevents:

Dirty Reads.

Allows:

- Non-repeatable Reads
- Phantom Reads

Default for many databases such as PostgreSQL and Oracle.

---

# Repeatable Read

Prevents:

- Dirty Reads
- Non-repeatable Reads

Phantom behavior depends on the database implementation. For example, MySQL InnoDB's implementation prevents many phantom scenarios using next-key locking, while other databases may differ.

---

# Serializable

Highest isolation.

Transactions behave as if executed one after another.

Maximum correctness.

Lowest concurrency.

---

# Isolation Level Comparison

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|-----------------|------------|---------------------|--------------|
| Read Uncommitted | ✅ Possible | ✅ Possible | ✅ Possible |
| Read Committed | ❌ Prevented | ✅ Possible | ✅ Possible |
| Repeatable Read | ❌ Prevented | ❌ Prevented | Depends on DB implementation |
| Serializable | ❌ Prevented | ❌ Prevented | ❌ Prevented |

---

# Choosing Isolation Levels

| Business Requirement | Recommended Level |
|----------------------|-------------------|
| Analytics | Read Committed |
| E-commerce | Read Committed / Repeatable Read |
| Inventory | Repeatable Read |
| Banking | Serializable or Carefully Designed Pessimistic Locking |
| Reporting | Snapshot Isolation / MVCC |

---

# Production Incident

## Oversold Inventory

Flash sale.

Inventory:

```
100 Units
```

Thousands of concurrent updates.

Application:

```
SELECT

↓

UPDATE
```

No locking.

Result:

```
132 Items Sold
```

---

## Root Cause

Lost updates.

---

## Solution

Atomic SQL update.

Example:

```sql
UPDATE inventory

SET quantity = quantity - 1

WHERE

product_id = ?

AND quantity > 0;
```

No separate read.

No lost update.

---

# Production Incident

## Banking Balance Error

Two ATM withdrawals.

Application logic:

```
Read

↓

Calculate

↓

Write
```

No row lock.

Balance incorrect.

---

## Solution

Pessimistic locking.

---

# Best Practices

- Prefer atomic SQL updates where possible.
- Choose the lowest isolation level that satisfies business correctness.
- Use optimistic locking for low-contention workloads.
- Use pessimistic locking when conflicts are common and correctness is paramount.
- Understand your database's MVCC implementation.
- Keep transactions short.

---

# Architect's Perspective

Application-level synchronization alone is **not sufficient**.

Every enterprise architect must also understand **database concurrency control**.

The database is often the final authority for shared business state.

A well-designed application and a poorly configured database isolation level can still produce inconsistent outcomes.

Architects should think in layers:

```text
Application Synchronization

↓

Database Concurrency Control

↓

Distributed Coordination
```

Each layer contributes to overall correctness.

The next section extends concurrency beyond a single database into **distributed systems**, where coordination spans multiple machines using technologies such as Redis, ZooKeeper, etcd, and leader election.

---

**End of Part 10**

The next section will cover:

- **Distributed Locks**
- **Redis Locks**
- **Redlock**
- **ZooKeeper**
- **etcd**
- **Leader Election**
- **Lease-Based Locks**
- **Distributed Coordination**
- **Cloud-Native Concurrency**
