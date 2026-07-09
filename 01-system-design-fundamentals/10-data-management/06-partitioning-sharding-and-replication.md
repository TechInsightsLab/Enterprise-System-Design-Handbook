
# 44. Data Partitioning

> **Partitioning divides a large dataset into smaller, manageable pieces while keeping the data logically part of the same database.**

As applications grow,

tables eventually become:

- Hundreds of millions of rows
- Billions of rows
- Terabytes of data

A single large table becomes increasingly difficult to:

- Query
- Backup
- Maintain
- Scale

Partitioning solves this problem.

---

# Why Partition Data?

Imagine:

Order Table

```
2 Billion Rows
```

Simple query:

```sql
SELECT *

FROM Orders

WHERE OrderDate = TODAY;
```

Without partitioning,

database may scan:

```
2 Billion Rows
```

Very expensive.

---

# With Partitioning

Orders split by month.

```text
Orders

├── Jan

├── Feb

├── Mar

├── Apr

└── ...
```

Today's query scans only:

```
Current Partition
```

Much faster.

---

# Partitioning Goals

Partitioning improves:

- Query performance
- Backup speed
- Maintenance
- Scalability
- Archiving
- Availability

---

# Logical View

Application still sees:

```text
Orders
```

Internally,

database stores:

```text
Orders_2024

Orders_2025

Orders_2026
```

Transparent to application.

---

# Types of Partitioning

Most relational databases support:

```text
Partitioning

├── Horizontal

├── Vertical

├── Range

├── List

├── Hash

└── Composite
```

---

# 45. Horizontal Partitioning

> **Horizontal Partitioning divides rows into multiple partitions while keeping the same columns.**

Every partition contains:

Same schema.

Different rows.

---

# Example

Orders Table

```text
Orders

↓

2024 Orders

↓

2025 Orders

↓

2026 Orders
```

Same columns.

Different records.

---

# Example

Before:

| OrderID | Date |
|----------|------|
|1|2024|
|2|2025|
|3|2026|

---

After:

```text
Partition 2024

Order 1

-----------------

Partition 2025

Order 2

-----------------

Partition 2026

Order 3
```

---

# Benefits

- Smaller indexes
- Faster scans
- Easier archival
- Better maintenance

---

# 46. Vertical Partitioning

> **Vertical Partitioning divides columns instead of rows.**

Frequently accessed columns stay together.

Rarely accessed columns move elsewhere.

---

# Example

Customer

```text
CustomerID

Name

Email

Address

Photo

Biography
```

Most requests need:

```
Name

Email
```

Not:

```
Biography
```

---

# Vertical Split

Customer Basic

```text
ID

Name

Email
```

Customer Details

```text
ID

Biography

Photo
```

Smaller records.

Better cache efficiency.

---

# Good Candidates

Move:

- Images
- Large text
- Documents
- Optional fields

to separate storage.

---

# Horizontal vs Vertical

| Horizontal | Vertical |
|-------------|-----------|
| Split Rows | Split Columns |
| Same Schema | Different Schemas |
| Better Scaling | Smaller Records |
| Better Large Tables | Better Wide Tables |

---

# 47. Range Partitioning

Rows assigned based on ranges.

---

# Example

Orders.

```text
2023

↓

Partition 1

----------------

2024

↓

Partition 2

----------------

2025

↓

Partition 3
```

---

# Common Examples

Partition by:

- Date
- Age
- Price
- Customer ID Range

---

# Benefits

Excellent for:

Time-series data.

---

# Drawback

Uneven traffic may create:

Hot partitions.

---

# 48. List Partitioning

Rows assigned using predefined values.

---

# Example

Country.

```text
India

↓

Partition A

----------------

USA

↓

Partition B

----------------

Japan

↓

Partition C
```

---

# Good Use Cases

- Country
- Region
- Status
- Tenant

---

# 49. Hash Partitioning

Hash function determines partition.

---

# Example

```
CustomerID

↓

Hash()

↓

Partition
```

---

Customer:

101

↓

Hash

↓

Partition 5

---

# Benefits

Even distribution.

Good load balancing.

---

# Drawback

Range queries become harder.

Example:

```
Customers

100

↓

500
```

May span many partitions.

---

# 50. Composite Partitioning

Combines multiple strategies.

Example:

```
Range

↓

Hash
```

---

Orders

```text
2025

↓

Hash(CustomerID)
```

Benefits from:

Both.

---

# Partition Pruning

Modern databases optimize queries.

Suppose:

```sql
WHERE

OrderDate

BETWEEN

Jan

AND

Feb
```

Database scans only:

January.

February.

Not every partition.

Called:

```
Partition Pruning
```

Huge performance improvement.

---

# 51. Database Sharding

> **Sharding distributes data across multiple independent database servers.**

Unlike partitioning,

shards may live on:

Different machines.

---

# Why Sharding?

Eventually:

One database server becomes:

- CPU bottleneck
- Memory bottleneck
- Storage bottleneck
- Network bottleneck

Vertical scaling ends.

Need:

Horizontal scaling.

---

# Architecture

```text
Application

↓

Shard Router

↓

Shard A

Shard B

Shard C
```

Data distributed.

---

# Example

Customers.

```text
1–1M

↓

Shard A

----------------

1M–2M

↓

Shard B

----------------

2M–3M

↓

Shard C
```

---

# Sharding Key

Most important decision.

Determines:

Where data lives.

---

# Good Sharding Keys

- Customer ID
- User ID
- Tenant ID
- Region

---

# Poor Sharding Keys

Example:

```
Status
```

Most rows:

```
ACTIVE
```

Creates:

Hot shard.

---

# Sharding Strategies

```text
Range

Hash

Directory

Geo

Tenant
```

---

# Range Sharding

```text
Users

1-1M

↓

Shard A

1M-2M

↓

Shard B
```

Simple.

May create hotspots.

---

# Hash Sharding

```text
Hash(UserID)

↓

Shard
```

Even distribution.

Poor range queries.

---

# Directory-Based Sharding

Lookup table stores:

```text
Customer

↓

Shard
```

Flexible.

Additional lookup required.

---

# Geo Sharding

Users stored near:

Physical location.

---

India

↓

Mumbai

---

Europe

↓

Frankfurt

---

USA

↓

Virginia

Lower latency.

---

# Tenant Sharding

Multi-tenant SaaS.

```text
Tenant A

↓

Shard A

----------------

Tenant B

↓

Shard B
```

Popular in SaaS platforms.

---

# Benefits

- Horizontal scalability
- Smaller databases
- Better throughput
- Independent growth

---

# Challenges

- Cross-shard joins
- Distributed transactions
- Rebalancing
- Global indexes
- Operational complexity

---

# Rebalancing

Suppose:

Shard A:

```
95%
```

Shard B:

```
5%
```

Need:

Move data.

Called:

```
Rebalancing
```

Complex operation.

---

# Hot Shards

Very common production problem.

---

Example.

Celebrity user.

Millions of requests.

Same shard overloaded.

---

Solutions

- Better sharding key
- Caching
- Replication
- Request distribution

---

# Partitioning vs Sharding

| Partitioning | Sharding |
|--------------|----------|
| Usually One Database | Multiple Databases |
| Mostly Logical | Physical Distribution |
| Easier Management | More Complex |
| Limited Scalability | Massive Scalability |

---

# 52. Replication

> **Replication creates multiple copies of data across different servers.**

Purpose:

- Availability
- Reliability
- Read scalability
- Disaster recovery

---

# Architecture

```text
Primary

↓

Replica 1

↓

Replica 2

↓

Replica 3
```

---

# Benefits

- Failover
- Backup
- Read scaling
- Geographic distribution

---

# Replication Types

```text
Synchronous

↓

Asynchronous

↓

Semi-Synchronous
```

---

# 53. Synchronous Replication

Primary waits until replicas confirm.

---

Workflow

```text
Write

↓

Replica ACK

↓

Commit
```

---

Advantages

- Strong consistency
- No data loss (assuming required replicas acknowledge)

---

Disadvantages

- Higher latency
- Slower writes
- Sensitive to network delays

---

# 54. Asynchronous Replication

Primary commits immediately.

Replica updates later.

---

Workflow

```text
Write

↓

Commit

↓

Replicate Later
```

---

Advantages

- Very fast
- Better throughput

---

Disadvantages

Temporary inconsistency.

Possible data loss if the primary fails before replicas receive recent writes.

---

# 55. Semi-Synchronous Replication

Compromise.

Primary waits for acknowledgment from one (or a configured minimum number of) replica(s),

not necessarily all.

Balances:

Performance.

Consistency.

---

# Comparison

| Type | Latency | Consistency | Data Loss Risk |
|------|----------|-------------|----------------|
| Synchronous | High | Strong | Lowest |
| Semi-Synchronous | Medium | Medium | Lower |
| Asynchronous | Low | Eventual | Higher |

---

# 56. Read Replicas

Read replicas improve:

Read throughput.

---

Architecture

```text
Application

↓

Load Balancer

↓

Replica A

Replica B

Replica C
```

Writes still go to:

Primary.

---

# Benefits

- Read scalability
- Analytics
- Reporting
- Backup

---

# Challenge

Replication lag.

Recently written data may not immediately appear on replicas.

Architects must decide which workloads can tolerate stale reads.

---

# 57. Multi-Region Data Architecture

Global applications require:

Data close to users.

---

Example

```text
India

↓

Mumbai DB

----------------

Europe

↓

Frankfurt DB

----------------

USA

↓

Virginia DB
```

Lower latency.

---

# Why?

Without regional databases,

Indian users access:

US database.

Latency:

Hundreds of milliseconds.

---

# Multi-Region Goals

- Lower latency
- Regional resilience
- Disaster recovery
- Regulatory compliance
- Better user experience

---

# 58. Geo-Partitioning

Data stored based on geography.

---

Example

European users.

↓

Europe.

---

Indian users.

↓

India.

---

Benefits

- Compliance
- Performance
- Locality

---

# Example

Customer.

Country:

India.

Automatically stored:

Mumbai Region.

---

# 59. Data Locality

> **Keep data physically close to the users or services that access it most frequently.**

---

Benefits

- Lower latency
- Reduced bandwidth
- Better cache efficiency
- Improved user experience

---

# Example

Gaming Platform.

Asian players.

↓

Singapore Region.

European players.

↓

Frankfurt Region.

---

# 60. Cross-Region Replication

Regions replicate data.

---

Architecture

```text
Mumbai

↓

Frankfurt

↓

Virginia
```

Each region receives copies.

---

Benefits

- Disaster recovery
- Global availability
- Regional failover

---

Challenges

- Replication lag
- Conflict resolution
- Increased cost
- Network latency

---

# Common Mistakes

### Partitioning Too Early

Small systems rarely need partitioning.

Introduce it when justified by scale.

---

### Poor Sharding Key

Creates hot shards and uneven load.

Choose keys based on access patterns.

---

### Assuming Read Replicas Are Instantly Consistent

Replication lag is real.

Applications must account for it.

---

### Confusing Partitioning with Sharding

Partitioning organizes data.

Sharding distributes ownership across multiple database instances.

---

# Architect's Perspective

As data grows,

the problem changes from:

> **"How do I store this data?"**

to

> **"How do I distribute this data efficiently?"**

A common progression is:

```text
Single Database

↓

Partitioning

↓

Read Replicas

↓

Sharding

↓

Multi-Region Deployment

↓

Globally Distributed Database
```

Each step introduces additional operational complexity.

Architects should move to the next stage **only when current limitations justify it**.

Scaling data architecture is not about adding more databases.

It is about **placing the right data in the right location, for the right users, with the right consistency guarantees.**

---

**End of Part 6**

The next part will cover:

- **CAP Theorem**
- **PACELC Theorem**
- **ACID**
- **BASE**
- **Consistency Models**
- **Strong vs Eventual Consistency**
- **Quorum Reads/Writes**
- **Distributed Database Trade-offs**
