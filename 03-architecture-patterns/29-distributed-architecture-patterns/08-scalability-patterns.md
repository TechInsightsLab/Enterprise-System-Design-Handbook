
# 8. Scalability Patterns

One of the primary goals.

Of distributed systems.

Is.

To scale.

As business grows.

Without.

Rewriting.

The application.

---

# What is Scalability?

> **Scalability is the ability of a system to handle increasing workloads by efficiently utilizing additional resources while maintaining acceptable performance.**

Scalability.

Does NOT mean.

Fast.

It means.

The system.

Can continue growing.

Without.

Breaking.

---

# Business Story

Imagine.

An online retailer.

Starts with.

```text
1,000 Users
```

Everything works.

After two years.

```text
5 Million Users
```

Now.

Problems appear.

- Database overloaded
- API latency increases
- CPU reaches 100%
- Memory exhausted
- Cache misses increase

Question.

Do we.

Buy.

A bigger server?

Or.

Redesign.

The architecture?

---

# Types of Scaling

There are two.

Fundamental approaches.

```text
Scaling

│

├── Vertical Scaling

└── Horizontal Scaling
```

---

# Vertical Scaling (Scale Up)

Increase.

Resources.

Of.

One machine.

```text
4 CPU

↓

8 CPU

↓

32 CPU

↓

128 CPU
```

Or.

```text
16 GB RAM

↓

64 GB RAM

↓

256 GB RAM
```

---

## Advantages

- Simple
- No application changes
- No distributed coordination
- Easy database migration

---

## Disadvantages

- Hardware limit
- Expensive
- Single point of failure
- Downtime during upgrades

---

# Horizontal Scaling (Scale Out)

Instead of.

One large server.

Use.

Many smaller servers.

```text
Server 1

+

Server 2

+

Server 3

+

Server 4
```

Traffic.

Distributed.

Across.

All servers.

---

# Advantages

- Virtually unlimited growth
- High availability
- Better fault tolerance
- Cloud friendly

---

# Challenges

- Distributed systems complexity
- Data consistency
- Load balancing
- Session management

---

# Scalability Pattern Classification

```text
Scalability Patterns

│

├── Cache Patterns

├── Read Scaling

├── Write Scaling

├── Data Partitioning

├── Content Distribution

├── Geo Distribution

└── Compute Scaling
```

---

# Choosing a Pattern

Ask.

```text
Slow Reads?

↓

Caching

────────────────────

Database Overloaded?

↓

Read Replicas

────────────────────

Database Too Large?

↓

Sharding

────────────────────

Static Content?

↓

CDN

────────────────────

Global Users?

↓

Geo Replication

────────────────────

CPU Bottleneck?

↓

Horizontal Scaling
```

---

# 8.1 Cache Aside Pattern

One of the most.

Widely used.

Caching patterns.

---

# Problem

Every request.

Hits.

The database.

```text
Client

↓

Application

↓

Database
```

Expensive.

Slow.

Database.

Becomes.

Bottleneck.

---

# Definition

> **Cache Aside loads data into the cache only when requested. If the cache misses, the application fetches data from the database, stores it in the cache, and returns it to the client.**

---

# Flow

```text
Client

↓

Application

↓

Cache

↓

Hit?

↓

Yes

↓

Return

────────────

No

↓

Database

↓

Cache

↓

Client
```

---

# Example

Customer.

Views.

Product.

```text
Product #100
```

Cache miss.

↓

Database.

↓

Redis.

↓

Future requests.

Served.

From cache.

---

# Advantages

- Simple
- Memory efficient
- Popular
- Easy implementation

---

# Challenges

- Cache misses
- Cache invalidation
- Cold start

---

# 8.2 Read Through Cache

Different.

From.

Cache Aside.

---

# Definition

> **Read Through Cache automatically loads missing data from the database without application involvement.**

---

Architecture.

```text
Client

↓

Cache

↓

Database
```

Application.

Never.

Reads.

Database directly.

---

# Benefits

- Simpler application code
- Centralized cache logic

---

# Drawbacks

- Cache provider.
Must support.

Read Through.

---

# Cache Aside vs Read Through

| Cache Aside | Read Through |
|--------------|--------------|
| Application loads cache | Cache loads data |
| More application logic | Less application logic |
| Most common | Less common |

---

# 8.3 Write Through Cache

Question.

What happens.

When data changes?

---

Solution.

Write.

To cache.

And database.

Together.

---

Architecture.

```text
Application

↓

Cache

↓

Database
```

Both.

Updated.

Immediately.

---

# Benefits

- Cache always current
- No stale reads

---

# Drawbacks

- Higher write latency
- Every write updates cache

---

# 8.4 Write Behind Cache (Write Back)

Different strategy.

---

# Definition

> **Write Behind updates the cache immediately and persists changes to the database asynchronously.**

---

Architecture.

```text
Application

↓

Cache

↓

Response

↓

Background Flush

↓

Database
```

---

# Advantages

- Extremely fast writes
- Reduced database load

---

# Risks

If cache crashes.

Before.

Flushing.

Data.

May be lost.

Requires.

Reliable persistence.

---

# 8.5 Refresh Ahead Cache

Popular.

For.

Frequently accessed.

Data.

---

Instead of.

Waiting.

For cache expiration.

Refresh.

Before.

Expiration.

---

Timeline.

```text
Cache

↓

About To Expire

↓

Background Refresh

↓

Client Never Sees Miss
```

---

# Good Use Cases

- Product catalog
- Exchange rates
- Weather
- Configuration
- Reference data

---

# Cache Pattern Comparison

| Pattern | Best For |
|-----------|----------|
| Cache Aside | General applications |
| Read Through | Managed cache platforms |
| Write Through | Strong consistency |
| Write Behind | High write throughput |
| Refresh Ahead | Frequently accessed data |

---

# 8.6 Read Replicas

Sometimes.

Writes.

Are small.

Reads.

Are enormous.

---

Example.

Amazon.

```text
Writes

10,000/sec

--------------------

Reads

2,000,000/sec
```

One database.

Cannot handle.

Both efficiently.

---

# Solution

Read Replicas.

---

Architecture.

```text
Primary Database

↓

Replication

↓

Replica 1

Replica 2

Replica 3
```

Writes.

Primary.

Reads.

Replicas.

---

# Benefits

- Higher read throughput
- Reduced primary load
- Better availability

---

# Challenges

Replication.

Is usually.

Asynchronous.

Meaning.

Read-after-write.

May.

Return stale data.

---

# 8.7 Database Sharding

Eventually.

One database.

Becomes.

Too large.

---

Question.

How do we.

Store.

100 TB.

Inside.

One server?

We don't.

---

# Definition

> **Sharding horizontally partitions data across multiple independent databases based on a shard key.**

---

Architecture.

```text
Users

↓

Shard Function

↓

Shard 1

Shard 2

Shard 3
```

---

# Example

Customers.

Partitioned.

By Customer ID.

```text
1–1M

↓

Shard A

-------------------

1M–2M

↓

Shard B

-------------------

2M–3M

↓

Shard C
```

---

# Shard Key

Critical decision.

Good examples.

- Customer ID
- Region
- Tenant ID

Bad examples.

- Country (uneven distribution)
- Status (hot shards)

---

# Advantages

- Unlimited growth
- Parallel processing
- Independent scaling

---

# Challenges

- Cross-shard joins
- Rebalancing
- Hot shards
- Operational complexity

---

# 8.8 Partitioning

Often confused.

With.

Sharding.

---

# Partitioning

Usually.

Means.

Splitting data.

Inside.

One database.

---

Example.

Orders.

Partitioned.

By month.

```text
Orders_2025

Orders_2026

Orders_2027
```

Same database.

Different partitions.

---

# Sharding vs Partitioning

| Partitioning | Sharding |
|--------------|----------|
| Within one database | Across multiple databases |
| Often transparent | Application aware |
| Easier | More complex |
| Limited scaling | Massive scaling |

---

# 8.9 CDN (Content Delivery Network)

Not every request.

Needs.

Application servers.

---

Static files.

Examples.

- Images
- Videos
- CSS
- JavaScript
- PDFs

---

Architecture.

```text
User

↓

Nearest CDN

↓

Origin Server
```

Most requests.

Never reach.

Origin.

---

# Benefits

- Lower latency
- Reduced bandwidth
- Better global performance

---

# Popular CDNs

- CloudFront
- Cloudflare
- Akamai
- Fastly
- Azure CDN

---

# 8.10 Geo-Replication

Global users.

Need.

Global performance.

---

Architecture.

```text
US

↓

Database

↓

Europe

↓

Replica

↓

Asia

↓

Replica
```

Users.

Read.

From.

Nearest region.

---

# Challenges

- Replication lag
- Conflict resolution
- Global consistency

---

# Pattern Relationships

Modern.

Large-scale systems.

Combine.

Many patterns.

```text
Client

↓

CDN

↓

Load Balancer

↓

Application

↓

Redis Cache

↓

Read Replica

↓

Primary Database

↓

Shards

↓

Geo Replicas
```

Each layer.

Improves.

Scalability.

---

# Example — Amazon Product Catalog

```text
Customer

↓

CloudFront CDN

↓

API Gateway

↓

Product Service

↓

Redis

↓

Read Replica

↓

Primary Database

↓

Sharded Database

↓

Global Replication
```

Millions.

Of users.

Served.

Using.

Multiple scalability patterns.

---

# Scalability Decision Matrix

| Problem | Pattern |
|----------|----------|
| Slow database reads | Cache Aside |
| Repeated cache misses | Refresh Ahead |
| Massive read traffic | Read Replicas |
| Massive dataset | Sharding |
| Large historical tables | Partitioning |
| Global static content | CDN |
| Global read latency | Geo Replication |
| CPU bottleneck | Horizontal Scaling |

---

# Common Misconceptions

### Misconception 1

> Caching eliminates the need for a database.

False.

Caches improve performance but remain temporary. The database is still the system of record.

---

### Misconception 2

> Read Replicas improve write performance.

False.

They reduce read load on the primary database but do not increase write throughput.

---

### Misconception 3

> Sharding should be introduced early.

False.

Sharding adds significant operational complexity and should be adopted only when simpler scaling techniques are insufficient.

---

### Misconception 4

> CDN only caches images.

False.

Modern CDNs cache HTML, APIs (when appropriate), videos, software downloads, and many other types of content.

---

# Interview Questions

### Q1

When should Cache Aside be preferred over Write Through?

**Answer**

Cache Aside is preferred for general-purpose applications because it minimizes cache storage and loads only frequently accessed data. Write Through is more appropriate when cache consistency is more important than write latency.

---

### Q2

Why are Read Replicas eventually consistent?

**Answer**

Most replication is asynchronous, so updates made to the primary database may take time to propagate to replicas.

---

### Q3

What is the difference between Sharding and Partitioning?

**Answer**

Partitioning divides data within a single database instance, while sharding distributes data across multiple independent database instances.

---

### Q4

Why is selecting a shard key difficult?

**Answer**

A poor shard key can create uneven data distribution, hot shards, expensive rebalancing, and poor query performance.

---

# Architect's Perspective

Scalability is achieved through **layered optimization**, not a single technique.

The most successful architectures progressively introduce caching, read replicas, partitioning, sharding, CDNs, geo-replication, and horizontal scaling as business demand grows.

Architects should always choose the **simplest scalability mechanism** that solves the current bottleneck rather than prematurely adopting complex distributed data strategies.

---

**End of Part 8**

The next part will cover **Cloud-Native Patterns**, including:

- Sidecar Pattern
- Ambassador Pattern
- Adapter Pattern
- Service Mesh
- Init Container
- Operator Pattern
- Control Plane vs Data Plane
- Kubernetes-native architectural patterns
