
# 43. Cache Consistency

> **Cache Consistency ensures that cached data accurately reflects the state of the underlying source of truth according to the application's consistency requirements.**

One of the biggest misconceptions is:

> "Caching is easy."

Reading from cache is easy.

Keeping cache **correct** is difficult.

---

# Why Cache Consistency Matters

Imagine:

Product Price

Database:

```
₹80,000
```

Redis:

```
₹75,000
```

Customer purchases product.

Which price should be charged?

Incorrect cache can directly impact business.

---

# The Fundamental Problem

Two copies exist.

```text
Database

↓

Cache
```

Whenever one changes,

the other must eventually change.

Synchronization becomes the challenge.

---

# Source of Truth

One architectural rule should always exist:

> **The database remains the system of record unless explicitly designed otherwise.**

Cache is:

Temporary.

Derived.

Replaceable.

Never treat cache as the primary business database for transactional systems.

---

# Cache Consistency Models

Different systems require different guarantees.

```text
Strong

↓

Read-After-Write

↓

Eventual

↓

Best Effort
```

---

# Strong Cache Consistency

After update,

every read returns:

Latest value.

---

Workflow

```text
Update Database

↓

Update Cache

↓

Return
```

Higher correctness.

Higher latency.

---

# Read-After-Write Consistency

User updates profile.

Immediately refreshes page.

Should always see:

Updated profile.

---

Example

```text
User

↓

Update Profile

↓

Refresh

↓

New Data
```

Important for user experience.

---

# Eventual Consistency

Cache updates later.

Small delay acceptable.

---

Example

Social Media.

Like count.

```
100

↓

101

↓

102
```

A short delay is acceptable.

---

# Best-Effort Consistency

Some systems simply:

Invalidate cache.

Eventually:

Reload.

Common for:

- Product Catalog
- Articles
- Blog Pages

---

# Choosing Consistency

| Data | Consistency |
|--------|-------------|
| Bank Balance | Strong |
| Shopping Cart | Read-after-write |
| Product Catalog | Eventual |
| News Articles | Best Effort |
| Recommendations | Eventual |

Business drives consistency.

---

# 44. Cache Synchronization

Whenever database changes,

cache must also change.

Synchronization strategies determine:

How.

---

# Strategy 1

Update Database.

↓

Update Cache.

---

# Strategy 2

Update Database.

↓

Delete Cache.

---

# Strategy 3

Update Database.

↓

Publish Event.

↓

Services Update Cache.

---

# Strategy 4

TTL expires.

↓

Reload later.

---

# Which Is Best?

Depends on:

- Freshness
- Performance
- Complexity

---

# 45. Cache Coherence

> **Cache Coherence ensures that multiple caches do not serve conflicting values for the same data.**

Problem.

Application A.

```
Price

₹100
```

Application B.

```
Price

₹120
```

Both cached.

Conflict.

---

# Architecture

```text
App A

↓

Local Cache

----------------

Redis

----------------

App B

↓

Local Cache
```

Local caches require synchronization.

---

# Common Solutions

- Pub/Sub
- Events
- Versioning
- TTL
- Explicit invalidation

---

# 46. Event-Driven Cache Invalidation

Modern microservices prefer:

Events.

Instead of direct cache updates.

---

# Workflow

```text
Update Product

↓

Database

↓

Publish Event

↓

Kafka

↓

Inventory Service

↓

Pricing Service

↓

Recommendation Service

↓

Invalidate Cache
```

Every service reacts independently.

---

# Benefits

- Loose coupling
- Independent scaling
- Multiple consumers
- Real-time updates

---

# Enterprise Example

Price changed.

Every dependent service receives:

```
ProductUpdated
```

Caches refreshed.

Automatically.

---

# 47. Redis Pub/Sub

Redis supports:

Publish.

Subscribe.

Messaging.

---

# Architecture

```text
Publisher

↓

Redis Channel

↓

Subscriber A

↓

Subscriber B

↓

Subscriber C
```

---

# Cache Invalidation Example

Application updates product.

Publishes.

```
ProductChanged
```

All application instances receive notification.

Local caches cleared.

---

# Advantages

- Very simple
- Fast
- Low latency

---

# Limitations

Messages are not durable.

If a subscriber is offline,

it misses the notification.

For reliable event delivery across services, systems often prefer Kafka or similar durable messaging platforms.

---

# 48. Kafka + CDC

Enterprise systems frequently combine:

```
Database

↓

CDC

↓

Kafka

↓

Consumers

↓

Invalidate Cache
```

---

# Example

Customer address updated.

CDC captures change.

Kafka publishes event.

All caches updated.

Eventually consistent.

---

# Why CDC?

Application developers may forget:

Cache invalidation.

CDC observes:

Database itself.

Reliable.

---

# Advantages

- Automatic
- Language independent
- Event-driven
- Scalable

---

# Enterprise Example

```text
PostgreSQL

↓

Debezium

↓

Kafka

↓

Customer Service

↓

Billing

↓

Shipping

↓

Analytics
```

Everyone stays synchronized.

---

# 49. Cache Replication

Distributed caches often replicate data.

---

Architecture

```text
Primary Redis

↓

Replica

↓

Replica
```

---

Benefits

- High Availability
- Failover
- Read Scaling

---

Challenges

Replication lag.

Stale reads possible.

---

# Multi-Region Cache

Global applications.

Need cache close to users.

---

Architecture

```text
India

↓

Redis

----------------

Europe

↓

Redis

----------------

USA

↓

Redis
```

Each region.

Local cache.

---

# Challenge

Product updated.

India.

How quickly should Europe know?

Trade-off:

Latency.

Consistency.

---

# Geo Cache Synchronization

Possible strategies.

- Async replication
- Event streaming
- Versioning
- Regional ownership

---

# Example

Product catalog.

Eventually consistent.

Bank balance.

Strong consistency.

Different solutions.

---

# 50. Near Cache Synchronization

Near cache:

Application memory.

Redis:

Shared cache.

Need synchronization.

---

Workflow

```text
Redis Updated

↓

Notification

↓

Application

↓

Invalidate Near Cache
```

Otherwise:

Application continues serving stale data.

---

# Technologies

- Redis Keyspace Notifications
- Hazelcast Near Cache invalidation
- Apache Ignite events
- Kafka

---

# 51. Cache Versioning

Instead of deleting cache,

change version.

---

Example.

```
product:100:v1
```

Update.

↓

```
product:100:v2
```

Applications automatically use:

Newest version.

Old entries naturally expire.

---

# Benefits

- No race conditions
- Better CDN support
- Easy rollback
- Safe deployments

---

# Example

Deployment.

```text
app.js

↓

app.v25.js
```

Browser downloads.

Fresh version.

---

# 52. Stale Data

Stale data means:

Cache.

Old.

Database.

New.

---

# Causes

- Replication lag
- Long TTL
- Missed invalidation
- Event failure
- Network partition

---

# Handling Stale Data

Options.

- Lower TTL
- Versioning
- Read-through
- Refresh Ahead
- Event-driven invalidation
- Read from primary for critical requests

---

# Stale-While-Revalidate (SWR)

A popular strategy for web applications.

Instead of blocking users,

serve slightly stale data.

Refresh in the background.

---

Workflow

```text
Request

↓

Serve Cached Response

↓

Background Refresh

↓

Update Cache
```

Users experience:

Very low latency.

---

# Good Use Cases

- News
- Blogs
- Product Catalog
- Public APIs

---

# Not Suitable

- Banking
- Payments
- Trading

---

# 53. Distributed Cache Consistency Challenges

As cache grows,

new challenges appear.

---

## Network Partition

Some nodes.

Receive updates.

Others.

Do not.

---

## Replica Lag

Primary updated.

Replica behind.

---

## Multiple Writers

Two services.

Update same key.

Conflict.

---

## Event Loss

Invalidation event.

Never delivered.

Stale cache persists.

---

# Mitigation Strategies

- Idempotent updates
- Version numbers
- Event replay
- Durable messaging
- Monitoring
- Short TTL for critical data

---

# Event Ordering

Suppose.

Two updates.

```
Price

₹100

↓

₹120
```

If events arrive:

Out of order.

Cache may contain:

₹100

Again.

---

# Solution

Include:

- Version
- Timestamp
- Sequence number

Consumers ignore:

Older events.

---

# Consistency vs Performance

Higher consistency usually means:

- More coordination
- More latency
- More network traffic

Higher performance often means:

- Longer TTL
- More stale data
- Fewer synchronization operations

Architects balance both.

---

# Common Mistakes

### Multiple Sources of Truth

Database.

Redis.

Application Memory.

All independently updated.

Eventually inconsistent.

Always define one authoritative source.

---

### Long TTL for Frequently Updated Data

Users receive stale information.

TTL should match business volatility.

---

### Missing Event Monitoring

Events stop flowing.

Cache remains stale.

Nobody notices.

Monitor event pipelines.

---

### Assuming Redis Replication Is Instant

Replication delay exists.

Critical reads may require the primary or another consistency strategy.

---

# Architect's Perspective

Cache consistency is where **system design becomes business design**.

The correct question is **not**:

> "How do we keep cache perfectly synchronized?"

Instead ask:

- What level of inconsistency can the business tolerate?
- Which users must always see the latest data?
- Which operations require read-after-write guarantees?
- Is lower latency worth temporary staleness?

Most successful internet-scale systems intentionally choose **eventual consistency** for non-critical data because it provides:

- Better scalability
- Higher availability
- Lower latency

while reserving **strong consistency** only for business-critical workflows.

The architect's job is to understand **where correctness is mandatory and where freshness is negotiable**.

---

**End of Part 7**

The next part will cover:

- **Cache Stampede (Dogpile Effect)**
- **Cache Avalanche**
- **Cache Penetration**
- **Hot Keys**
- **Cold Cache**
- **Cache Breakdown**
- **Bloom Filters**
- **Distributed Locking**
- **SingleFlight / Request Coalescing**
- **Production Failure Scenarios**
