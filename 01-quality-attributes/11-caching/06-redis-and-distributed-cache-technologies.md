
# 34. Redis

> **Redis is an in-memory data structure store used as a cache, message broker, session store, rate limiter, and lightweight database.**

Redis stands for:

```
REmote

DIctionary

Server
```

Unlike traditional caches,

Redis provides:

- Rich data structures
- Replication
- Persistence
- Pub/Sub
- Transactions
- Lua scripting
- Clustering

This makes Redis far more than "just a cache."

---

# Why Redis?

Imagine an application serving:

```
500,000 Requests/sec
```

Every request hitting PostgreSQL.

Database becomes overloaded.

Instead,

store frequently accessed data in Redis.

```text
Application

↓

Redis

↓

Database
```

Now most reads never reach the database.

---

# Redis Characteristics

- In-memory
- Extremely fast
- Single-threaded command execution (core execution model)
- Network accessible
- Rich data structures
- Persistence options
- Replication
- Clustering

---

# Why Is Redis Fast?

Several reasons contribute:

- Data stored in RAM
- Efficient internal data structures
- Event-driven architecture
- Minimal locking in the command execution path
- Optimized network protocol

---

# Typical Latency

Approximate response time:

```
0.2 ms

to

1 ms
```

Much faster than most relational databases.

---

# Redis Architecture

```text
Application

↓

Redis

↓

Memory

↓

(Optional Persistence)
```

Memory is the primary storage.

Persistence is optional.

---

# Enterprise Use Cases

Redis is commonly used for:

- Cache
- Sessions
- Shopping Cart
- Rate Limiting
- Leaderboards
- Distributed Locking
- Pub/Sub
- Queues
- Feature Flags
- API Response Cache

---

# Redis as a Cache

```text
Client

↓

Application

↓

Redis

↓

PostgreSQL
```

Most enterprise deployments use Redis in this role.

---

# Redis Memory Model

Redis stores:

Everything.

In RAM.

---

Benefits:

- Very low latency
- High throughput

Trade-off:

RAM is expensive.

Memory planning is important.

---

# 35. Redis Data Structures

One reason Redis is so popular:

It supports multiple data structures.

---

# Overview

```text
Redis

├── String

├── Hash

├── List

├── Set

├── Sorted Set

├── Bitmap

├── HyperLogLog

├── Stream

└── Geospatial
```

Each solves different business problems.

---

# String

Simplest type.

Example:

```
product:100

↓

Laptop
```

---

Typical Uses

- Cache
- Tokens
- Configuration
- Sessions

---

# Hash

Stores field-value pairs.

Example.

```text
Customer

↓

Name

↓

Email

↓

Phone
```

Similar to:

JSON object.

---

Example

```text
customer:101

↓

name

↓

John
```

---

Uses

- User Profile
- Product
- Metadata

---

# List

Ordered collection.

---

Example.

```text
Task1

↓

Task2

↓

Task3
```

---

Uses

- Queue
- Timeline
- Recent Activity

---

# Set

Unique values.

No duplicates.

---

Example.

```
User Roles

↓

ADMIN

USER

AUDITOR
```

---

Uses

- Tags
- Permissions
- Membership

---

# Sorted Set (ZSET)

Maintains:

Unique elements.

Sorted by score.

---

Example.

Leaderboard.

```text
Player

↓

Score
```

Automatically sorted.

---

Uses

- Ranking
- Gaming
- Trending
- Recommendations

---

# Bitmap

Stores:

Bits.

Extremely memory efficient.

---

Example.

Daily login.

```
User 101

↓

Logged In

↓

1
```

---

Uses

- Login tracking
- Feature flags
- Attendance

---

# HyperLogLog

Estimates:

Unique count.

Using very little memory.

---

Example.

Unique visitors.

```
100 Million Users
```

Approximate count.

Tiny memory.

---

Uses

- Analytics
- Visitor counting
- Cardinality estimation

---

# Stream

Append-only log.

---

Example.

```text
Order Created

↓

Payment Received

↓

Shipment Started
```

---

Uses

- Event streaming
- Messaging
- Notifications

---

# Geospatial

Stores:

Latitude.

Longitude.

---

Uses

- Nearby Drivers
- Delivery Tracking
- Restaurants Nearby

---

# Choosing Data Structures

| Requirement | Redis Structure |
|--------------|----------------|
| Simple Value | String |
| Object | Hash |
| Queue | List |
| Unique Items | Set |
| Ranking | Sorted Set |
| Approximate Counting | HyperLogLog |
| Events | Stream |
| Location | Geospatial |

---

# 36. Redis Persistence

Redis is memory-first.

But memory alone is insufficient.

Power failures happen.

Servers restart.

Persistence protects data.

---

# Two Persistence Methods

```text
Redis

↓

RDB

↓

AOF
```

Can also be combined.

---

# RDB (Redis Database Snapshot)

Redis periodically creates:

Snapshots.

---

Workflow.

```text
Memory

↓

Snapshot

↓

Disk
```

---

Example.

Every:

```
5 Minutes
```

Snapshot created.

---

Advantages

- Compact files
- Faster restart
- Lower write overhead

---

Disadvantages

Recent writes between snapshots may be lost if Redis crashes.

---

# AOF (Append Only File)

Every write command appended to a log.

---

Workflow.

```text
SET

↓

Append Log

↓

Disk
```

---

Recovery.

Replay log.

Restore state.

---

Advantages

- Better durability
- Less data loss

---

Disadvantages

- Larger files
- More disk I/O
- Slower recovery than snapshots

---

# RDB vs AOF

| RDB | AOF |
|------|-----|
| Snapshot | Every Write Logged |
| Faster Recovery | Better Durability |
| Smaller Files | Larger Files |
| Less Disk Usage | More Disk Usage |

---

# Enterprise Recommendation

Many production deployments enable:

```
RDB

+

AOF
```

Balancing recovery speed and durability.

---

# 37. Redis Replication

Replication creates:

Multiple Redis copies.

---

Architecture

```text
Primary

↓

Replica 1

↓

Replica 2
```

---

Writes.

Go to:

Primary.

---

Reads.

May go to:

Replicas.

---

Benefits

- High Availability
- Read Scaling
- Backup
- Disaster Recovery

---

# Replication Workflow

```text
Application

↓

Primary

↓

Replicate

↓

Replica
```

---

# Replication Lag

Updates require time.

Replicas may briefly return older values.

Applications requiring immediate read-after-write consistency should account for this.

---

# 38. Redis Sentinel

> **Redis Sentinel provides monitoring, automatic failover, and leader election for Redis deployments.**

Without Sentinel.

Primary crashes.

Application stops.

---

With Sentinel.

```text
Sentinel

↓

Detect Failure

↓

Promote Replica

↓

Update Clients
```

Recovery automated.

---

# Sentinel Responsibilities

- Monitor
- Detect failures
- Elect new primary
- Notify clients

---

# Architecture

```text
Sentinel

↓

Primary

↓

Replica

↓

Replica
```

Typically,

multiple Sentinel instances are deployed for fault tolerance.

---

# Benefits

- Automatic failover
- Health monitoring
- High availability

---

# Limitations

Sentinel does **not** provide:

Horizontal data partitioning.

Only high availability.

---

# 39. Redis Cluster

> **Redis Cluster distributes data across multiple Redis nodes using sharding.**

Unlike Sentinel,

Cluster supports:

Horizontal scaling.

---

# Architecture

```text
Application

↓

Cluster

↓

Node A

↓

Node B

↓

Node C
```

Data distributed.

---

# Hash Slots

Redis Cluster divides keyspace into:

```
16,384

Hash Slots
```

Each node owns:

Some slots.

---

Example

```text
Slot

1-5000

↓

Node A

--------------

5001-10000

↓

Node B

--------------

10001-16384

↓

Node C
```

---

# Key Routing

Application stores:

```
user:101
```

Hash determines:

Responsible node.

---

# Benefits

- Horizontal scaling
- High throughput
- Automatic routing
- Failover support

---

# Limitations

Some multi-key operations require keys to reside in the same hash slot.

Architects should design keys accordingly.

---

# Sentinel vs Cluster

| Sentinel | Cluster |
|-----------|----------|
| High Availability | High Availability + Horizontal Scaling |
| No Sharding | Automatic Sharding |
| Single Primary | Multiple Primaries |
| Simpler | More Complex |

---

# 40. Memcached

> **Memcached is a lightweight distributed in-memory key-value cache designed for simplicity and speed.**

Compared to Redis,

Memcached supports:

Primarily simple key-value caching.

---

# Characteristics

- Memory only
- No persistence
- Simple architecture
- Multi-threaded
- Extremely fast

---

# Advantages

- Easy to operate
- Very high throughput
- Low memory overhead

---

# Limitations

- No replication
- Limited data structures
- No persistence
- No Pub/Sub
- No Streams

---

# Typical Uses

- HTML Fragments
- API Responses
- Database Results
- Session Cache

---

# Redis vs Memcached

| Redis | Memcached |
|---------|-----------|
| Rich Data Structures | Simple Key-Value |
| Persistence | No Persistence |
| Replication | No Built-in Replication |
| Pub/Sub | No |
| Streams | No |
| More Features | Simpler |

---

# 41. Hazelcast

Hazelcast is an:

```
In-Memory Data Grid (IMDG)
```

Rather than:

Simple cache.

---

# Features

- Distributed Map
- Near Cache
- Distributed Lock
- Distributed Executor
- Eventing

---

# Enterprise Uses

- Java Applications
- Microservices
- Distributed Computing

---

# Advantages

- Embedded mode
- Client-server mode
- Near Cache
- Java-friendly

---

# 42. Apache Ignite

Apache Ignite extends beyond caching.

Provides:

- Distributed cache
- SQL
- Compute grid
- Data grid
- In-memory processing

---

# Enterprise Uses

- Financial systems
- Real-time analytics
- Distributed processing

---

# Cache Technology Comparison

| Technology | Best For |
|------------|----------|
| Redis | General-purpose distributed cache |
| Memcached | Simple caching |
| Hazelcast | Java distributed data grid |
| Apache Ignite | In-memory computing |
| Caffeine | Local application cache |

---

# Choosing the Right Technology

Choose Redis when:

- Shared cache required
- Rich data structures needed
- Sessions
- Rate limiting
- Pub/Sub
- Distributed locking

---

Choose Memcached when:

- Simple caching
- Very lightweight deployment
- No persistence required

---

Choose Caffeine when:

- Single JVM
- Local cache
- Extremely low latency

---

Choose Hazelcast when:

- Java ecosystem
- Distributed data grid
- Near cache

---

Choose Apache Ignite when:

- Large in-memory datasets
- Compute close to data
- SQL over distributed memory

---

# Common Mistakes

### Treating Redis as Permanent Storage

Redis is primarily a cache and in-memory data store.

Design accordingly.

---

### Ignoring Persistence Configuration

Unexpected restart.

Cache lost.

Understand RDB and AOF trade-offs.

---

### No High Availability

Single Redis instance.

Single point of failure.

Use Sentinel or Cluster where appropriate.

---

### Huge Keys

Avoid storing:

Massive objects.

Prefer smaller,

well-defined cache entries.

---

### Poor Key Naming

Bad.

```
12345
```

Good.

```
product:12345

user:100:cart

session:abc123
```

Improves maintainability.

---

# Architect's Perspective

Choosing a cache technology is **not** simply a feature comparison.

It depends on:

- Workload characteristics
- Data structures
- Availability requirements
- Scaling needs
- Operational maturity
- Cost

For many enterprise applications:

- **Redis** is the default choice because of its versatility.
- **Caffeine** excels as a local in-process cache.
- **Memcached** remains effective for simple distributed caching.
- **Hazelcast** and **Apache Ignite** are strong choices when applications need distributed in-memory data grids rather than only key-value caching.

An architect's responsibility is not to adopt the most feature-rich technology.

It is to choose **the simplest technology that satisfies current and foreseeable business requirements.**

---

**End of Part 6**

The next part will cover:

- **Cache Consistency**
- **Distributed Cache Synchronization**
- **Cache Coherence**
- **Event-Driven Cache Invalidation**
- **Redis Pub/Sub**
- **Kafka + CDC**
- **Multi-Region Cache**
- **Cache Replication**
- **Near Cache Synchronization**
- **Consistency Trade-offs**
