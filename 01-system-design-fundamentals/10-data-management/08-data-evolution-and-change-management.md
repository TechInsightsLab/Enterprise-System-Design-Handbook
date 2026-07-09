
# Architect's Perspective (Continued)

Enterprise architects should never ask:

> **"Should I use ACID or BASE?"**

Instead ask:

- What business invariant must always hold?
- Can users tolerate stale data?
- What happens if two users update simultaneously?
- What is the cost of inconsistency?
- What is the cost of unavailability?

For example:

| Business Domain | Preferred Model |
|-----------------|-----------------|
| Banking | Strong Consistency (ACID) |
| Payments | Strong Consistency |
| Inventory | Strong or Carefully Controlled Eventual Consistency |
| Shopping Cart | Eventual Consistency |
| Product Catalog | Eventual Consistency |
| Social Media | Eventual Consistency |
| Analytics | Eventual Consistency |

The best architecture is the one that provides **exactly the level of consistency the business requires—not more, not less.**

---

# 71. Consensus in Distributed Databases

> **Consensus is the process by which multiple distributed nodes agree on a single value or decision, even in the presence of failures.**

Distributed databases cannot rely on:

- Shared memory
- Shared clocks
- Instant communication

Instead,

they use consensus algorithms.

---

# Why Consensus?

Imagine:

Three database nodes.

```text
Node A

↓

Node B

↓

Node C
```

Client writes:

```
Balance = ₹10,000
```

Node A receives the request.

Question:

Should Nodes B and C also accept it?

Consensus decides.

---

# Problems Consensus Solves

- Leader Election
- Distributed Locking
- Metadata Updates
- Cluster Membership
- Configuration Changes
- Transaction Coordination

---

# Without Consensus

Node A:

```
Balance = ₹10,000
```

Node B:

```
Balance = ₹8,000
```

Node C:

```
Balance = ₹11,000
```

Which value is correct?

Business cannot continue.

---

# Popular Consensus Algorithms

| Algorithm | Common Usage |
|------------|--------------|
| Raft | etcd, Consul |
| Paxos | Google Chubby, Spanner (inspired variants) |
| Zab | ZooKeeper |
| Viewstamped Replication | Research / Distributed Systems |

---

# Raft (High-Level)

Raft simplifies distributed consensus.

Each cluster contains:

```text
Leader

↓

Followers

↓

Candidate
```

Leader handles writes.

Followers replicate data.

---

# Leader Failure

Leader crashes.

Followers hold an election.

New leader selected.

Business continues.

---

# Why Consensus Matters

Without consensus,

distributed databases cannot safely:

- Elect leaders
- Replicate writes
- Recover after failures

Consensus is the foundation of modern distributed databases.

---

# 72. Write-Ahead Logging (WAL)

> **Write-Ahead Logging ensures durability by recording changes before modifying the actual database pages.**

Most enterprise databases implement WAL.

---

# Workflow

```text
Application

↓

Transaction

↓

Write Log

↓

Commit

↓

Update Data File
```

Notice:

Log written first.

Hence:

Write-Ahead Log.

---

# Crash Scenario

Suppose:

Database crashes after:

```
Commit
```

but before:

Data page updated.

After restart:

Database replays log.

Committed transaction recovered.

---

# Benefits

- Crash recovery
- Durability
- Faster commits
- Incremental backup
- Replication support

---

# Common Databases Using WAL

- PostgreSQL
- MySQL (Redo Log)
- SQL Server (Transaction Log)
- Oracle (Redo Log)

Different implementations.

Same principle.

---

# 73. Checkpoints

Constantly replaying years of logs would be slow.

Databases periodically create:

```
Checkpoint
```

---

# Workflow

```text
Transactions

↓

Checkpoint

↓

Continue Logging
```

Recovery starts from:

Latest checkpoint.

Much faster.

---

# Recovery

Without checkpoint:

Replay:

```
500 GB Logs
```

With checkpoint:

Replay:

```
2 GB Logs
```

Huge improvement.

---

# 74. Change Data Capture (CDC)

> **Change Data Capture captures every change made to a database and publishes it for downstream systems.**

Instead of repeatedly querying:

```
Has Anything Changed?
```

CDC streams changes.

---

# Example

Order Created.

```text
Database

↓

CDC

↓

Kafka

↓

Inventory

↓

Analytics

↓

Notifications
```

Every interested system receives the event.

---

# Why CDC?

Traditional polling:

```text
Application

↓

SELECT Every Minute
```

Wasteful.

CDC publishes only changes.

---

# Benefits

- Near real-time integration
- Reduced database load
- Event-driven architecture
- Data synchronization

---

# Popular CDC Tools

| Tool | Description |
|------|-------------|
| Debezium | Open-source CDC |
| Oracle GoldenGate | Enterprise replication |
| SQL Server CDC | Built-in |
| AWS DMS | Migration + CDC |

---

# Enterprise Example

Customer updates address.

CDC publishes:

```
CustomerUpdated
```

Services update:

- CRM
- Billing
- Shipping
- Analytics

Automatically.

---

# 75. Event Sourcing

> **Event Sourcing stores every state change as an immutable event instead of storing only the latest state.**

Traditional database:

```
Balance

↓

₹5000
```

Only current state stored.

---

# Event Sourcing

Store:

```text
Account Opened

↓

Deposit ₹5000

↓

Withdraw ₹1000

↓

Deposit ₹2000
```

Current balance calculated from events.

---

# Advantages

- Complete audit history
- Easy replay
- Time travel
- Debugging
- Event-driven integration

---

# Challenges

- More storage
- Event replay complexity
- Versioning
- Learning curve

---

# Suitable Domains

- Banking
- Trading
- Logistics
- Insurance
- Compliance-heavy systems

---

# 76. Snapshotting

Event replay becomes expensive.

Suppose:

```
10 Million Events
```

Need current balance.

Replaying all events is slow.

---

# Solution

Store periodic snapshots.

Example:

```text
Events

↓

Snapshot

↓

More Events

↓

Snapshot
```

Recovery starts from snapshot.

Only recent events replayed.

---

# Example

Snapshot:

```
Balance

₹95,000
```

Replay:

Last:

```
50 Events
```

instead of:

```
5 Million
```

---

# 77. Soft Delete vs Hard Delete

Deleting data is a business decision.

---

# Hard Delete

Record removed permanently.

```sql
DELETE

FROM Customer
```

Gone forever.

---

# Soft Delete

Record marked inactive.

```text
Deleted = TRUE
```

Still exists.

---

# Advantages

- Recovery
- Auditing
- Compliance
- Historical reporting

---

# Disadvantages

- Larger tables
- Query filtering required
- Additional maintenance

---

# Enterprise Practice

Business entities such as:

- Customers
- Orders
- Payments
- Invoices

are often soft deleted or archived rather than permanently removed.

---

# 78. Data Versioning

Business data evolves.

Schemas evolve.

APIs evolve.

Applications evolve.

Data must evolve too.

---

# Example

Customer V1

```text
Name

Email
```

Later.

Customer V2

```text
Name

Email

Phone
```

Older records remain valid.

---

# Versioning Approaches

- Schema versioning
- Record versioning
- API versioning
- Event versioning

---

# Benefits

- Backward compatibility
- Easier migrations
- Safer deployments

---

# 79. Schema Evolution

Schemas change over time.

Examples:

Add column.

Rename column.

Split table.

Merge table.

---

# Safe Schema Evolution

Preferred sequence:

```text
Add New Column

↓

Deploy Application

↓

Populate Data

↓

Remove Old Column
```

Avoid breaking existing applications.

---

# Unsafe Migration

```text
Rename Column

↓

Deploy
```

Older application version fails immediately.

---

# Best Practices

- Backward compatible changes first
- Avoid destructive changes during deployment
- Support rolling deployments
- Use feature flags when appropriate

---

# Common Mistakes

### Deleting Historical Data Too Early

Business later requires:

Audit.

Cannot recover.

---

### Direct Schema Changes in Production

Large table.

```
ALTER TABLE
```

May lock table.

Production outage.

Plan carefully.

---

### Ignoring Event Versioning

Consumers cannot process older events.

Distributed systems fail.

---

### Replaying Millions of Events

Without snapshots,

startup becomes extremely slow.

---

# Architect's Perspective

Enterprise data management extends beyond storage.

Architects must also design:

- How data changes
- How changes are propagated
- How history is preserved
- How failures are recovered
- How schemas evolve safely

Modern enterprise systems increasingly treat **data changes as first-class events**, enabling:

- Real-time integration
- Event-driven architecture
- Auditability
- Replayability
- Analytics

This is why technologies such as:

- WAL
- CDC
- Event Sourcing
- Snapshots
- Versioning

have become fundamental building blocks of modern distributed systems.

---

**End of Part 8**

The next part will cover:

- **Backup Strategies**
- **Restore Strategies**
- **Point-in-Time Recovery (PITR)**
- **Disaster Recovery (DR)**
- **RPO & RTO**
- **Data Archiving**
- **Data Retention Policies**
- **Data Lifecycle Management**
- **Enterprise Recovery Planning**
