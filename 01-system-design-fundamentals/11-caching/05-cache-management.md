
# 25. Time-To-Live (TTL)

> **Time-To-Live (TTL) defines how long a cached object remains valid before it automatically expires.**

TTL is one of the most fundamental concepts in caching.

Without TTL,

cache grows indefinitely.

Data becomes stale.

Memory eventually fills up.

---

# Why TTL Exists

Imagine:

Exchange Rate.

```
USD → INR
```

Cached forever.

Tomorrow:

Exchange rate changes.

Users continue receiving:

Old data.

Business decisions become incorrect.

TTL prevents this.

---

# Architecture

```text
Cache Entry

↓

Created

↓

Valid

↓

TTL Expires

↓

Removed

↓

Reloaded
```

---

# Example

Product Details.

```text
TTL

=

30 Minutes
```

For 30 minutes:

Serve from cache.

After:

Reload.

---

# Example Timeline

```text
12:00

↓

Cached

↓

12:15

↓

Still Valid

↓

12:30

↓

Expires

↓

12:31

↓

Reload
```

---

# Advantages

- Automatic cleanup
- Prevents stale data
- Controls memory usage
- Simplifies cache management

---

# Disadvantages

- Too short → Frequent cache misses
- Too long → Stale data

Choosing TTL is an architectural decision.

---

# Choosing TTL

Ask:

**How often does this data change?**

---

Static Configuration

```
24 Hours
```

---

Country List

```
Several Days
```

---

Weather

```
5 Minutes
```

---

Stock Prices

```
Seconds
```

---

Real-time Trading

```
May Not Cache
```

---

# TTL Selection Matrix

| Data Type | Suggested TTL |
|------------|---------------|
| Feature Flags | Minutes |
| Product Catalog | Minutes to Hours |
| User Profile | Minutes |
| Exchange Rates | Minutes |
| Country List | Days |
| Configuration | Hours |
| Product Images | Days |
| API Tokens | Depends on token lifetime |

There is no universal TTL.

Business freshness determines TTL.

---

# Absolute TTL

Object expires at:

Fixed duration.

Example.

```
30 Minutes
```

Regardless of usage.

---

# Sliding TTL

Expiration extends whenever the object is accessed.

Example.

```
30 Minutes

↓

Read

↓

Another 30 Minutes
```

Popular for:

Sessions.

---

# Comparison

| Absolute TTL | Sliding TTL |
|---------------|-------------|
| Fixed lifetime | Extends on access |
| Simpler | Better for active sessions |
| Predictable | More dynamic |

---

# Example

Shopping Cart.

Inactive:

```
30 Minutes
```

Every click.

TTL resets.

Session remains active.

---

# 26. Cache Expiration

TTL determines:

When an item expires.

Expiration determines:

What happens afterward.

---

# Workflow

```text
Cache Entry

↓

Expired

↓

Removed

↓

Next Request

↓

Reload
```

---

# Lazy Expiration

Expired objects remain until accessed.

When requested,

cache notices:

Expired.

Removes it.

Reloads.

---

# Active Expiration

Background process periodically removes expired entries.

Keeps memory clean.

Redis uses active expiration in addition to checking expiration during key access.

---

# Advantages

- Better memory usage
- Less stale data
- Automatic cleanup

---

# 27. Cache Invalidation

> **Cache Invalidation removes or updates cached data when the underlying source changes.**

One of the hardest problems in software engineering.

A famous saying:

> "There are only two hard things in Computer Science:
> Cache invalidation and naming things."

---

# Why?

Database changes.

Cache still contains:

Old value.

Users receive:

Incorrect data.

---

# Example

Database.

```
Price

₹50,000
```

Cache.

```
₹48,000
```

Wrong.

---

# Basic Workflow

```text
Database Update

↓

Invalidate Cache

↓

Next Read

↓

Reload
```

---

# Invalidation Strategies

```text
TTL

↓

Manual

↓

Event Driven

↓

Version Based

↓

Write Through

↓

Write Around
```

---

# TTL-Based Invalidation

Simplest.

Wait until:

TTL expires.

Reload.

---

Advantages

Easy.

---

Disadvantages

Temporary stale data.

---

# Manual Invalidation

Application explicitly removes cache.

Example.

```java
cache.remove(productId);
```

Next request.

Reloads.

---

# Advantages

Immediate.

---

Disadvantages

Developer responsibility.

Easy to forget.

---

# Event-Driven Invalidation

Modern microservices prefer:

Events.

---

Workflow

```text
Database Updated

↓

Kafka Event

↓

Inventory

↓

Product Service

↓

Cache Cleared
```

Every service updates automatically.

---

# Benefits

- Near real-time
- Loose coupling
- Scalable

---

# Version-Based Invalidation

Instead of removing cache,

change version.

Example.

```
Product

Version

5
```

Updated.

```
Version

6
```

New cache key.

---

Old cache naturally becomes unused.

---

# Example

```
product:100:v5
```

↓

Update.

↓

```
product:100:v6
```

No stale reads.

---

# Advantages

- No race conditions
- Simpler deployments
- CDN friendly

---

# Write-Through Invalidation

Whenever data changes:

Database.

↓

Cache updated immediately.

---

Fresh data.

No stale reads.

---

# Write-Around

Writes bypass cache.

Database updated directly.

Cache updated only after next read.

Useful when freshly written data is unlikely to be read immediately.

---

# Comparison

| Strategy | Freshness | Complexity |
|-----------|-----------|------------|
| TTL | Medium | Low |
| Manual | High | Medium |
| Event-Driven | High | High |
| Version-Based | High | Medium |
| Write-Through | High | Medium |
| Write-Around | Medium | Low |

---

# 28. Cache Eviction

> **Eviction is the process of removing cached entries when memory becomes full.**

Cache memory is limited.

Eventually:

Something must be removed.

---

# Workflow

```text
Memory Full

↓

Eviction Policy

↓

Remove Entry

↓

Store New Entry
```

---

# Goals

Eviction should remove:

Least valuable objects.

Keep:

Most useful objects.

---

# Common Policies

```text
LRU

↓

LFU

↓

FIFO

↓

Random
```

---

# 29. Least Recently Used (LRU)

> **LRU removes the item that has not been accessed for the longest time.**

Assumption:

Recently used items.

Likely used again.

---

# Example

Access order.

```
A

B

C

D
```

Access.

```
A

Again
```

Order becomes.

```
B

C

D

A
```

Need space.

Remove:

```
B
```

Oldest unused.

---

# Advantages

- Excellent general-purpose policy
- Simple
- Predictable

---

# Enterprise Uses

Redis supports LRU-based eviction modes.

Many application cache libraries also implement LRU.

---

# 30. Least Frequently Used (LFU)

> **LFU removes the item that has been accessed the fewest times.**

Popularity matters.

---

# Example

```
Product A

1000 Reads

----------------

Product B

3 Reads
```

Evict:

```
Product B
```

---

# Advantages

Better for:

Highly skewed workloads.

---

# Disadvantages

Requires tracking access counts.

Slightly more overhead.

---

# Example

Trending products.

Remain cached.

Long time.

---

# 31. First-In First-Out (FIFO)

Removes:

Oldest inserted object.

---

Example.

Inserted.

```
A

↓

B

↓

C

↓

D
```

Memory full.

Remove:

```
A
```

Regardless of usage.

---

# Advantages

Very simple.

---

# Disadvantages

Frequently used items may still be removed.

---

# 32. Random Replacement

Randomly selects an object.

Removes it.

---

Advantages.

Very simple.

Low overhead.

---

Disadvantages.

May evict:

Most popular item.

Rarely used for primary enterprise caches.

---

# Policy Comparison

| Policy | Decision Based On | Typical Quality |
|----------|------------------|-----------------|
| LRU | Recent usage | Excellent |
| LFU | Usage frequency | Excellent for skewed access |
| FIFO | Insertion order | Fair |
| Random | Random selection | Basic |

---

# 33. Memory Management

Caches are memory-bound systems.

Memory is:

Fast.

Expensive.

Limited.

---

# Memory Planning

Architects estimate:

```
Objects

×

Average Size

×

Growth

×

Safety Margin
```

---

# Example

Average Product.

```
2 KB
```

Products.

```
1 Million
```

Memory.

```
~2 GB
```

Plus overhead.

Always account for metadata and fragmentation.

---

# Object Size Matters

Caching:

Entire objects.

Consumes more memory.

Sometimes cache only:

- IDs
- Frequently used fields
- Computed values

---

# Compression

Some caches support compression.

Benefits.

- Lower memory
- More objects

Trade-off.

- Higher CPU usage

Choose based on workload.

---

# Cache Sizing

Questions to ask:

- How much memory is available?
- What hit ratio is required?
- Which data provides the highest business value?
- What happens when memory is exhausted?

---

# Monitoring Evictions

Important metrics:

- Eviction rate
- Expired keys
- Memory usage
- Hit ratio
- Miss ratio

Frequent evictions may indicate:

Cache too small.

Or:

Poor eviction policy.

---

# Common Mistakes

### Infinite TTL

Objects never expire.

Eventually become stale.

---

### One TTL for Everything

Different datasets change at different rates.

Choose TTL per data type.

---

### Ignoring Evictions

Applications suddenly experience:

Higher database traffic.

Investigate eviction spikes.

---

### Cache Without Monitoring

Without metrics,

architects cannot determine:

- Whether caching is effective
- Whether memory is sufficient
- Whether hit ratio is improving

---

# Architect's Perspective

A cache is **not simply a key-value store**.

It is a **managed memory system**.

Architects must answer four critical questions:

1. **How long should data remain valid?** (TTL)
2. **When should stale data be removed?** (Invalidation)
3. **What should happen when memory is full?** (Eviction)
4. **How much memory is required?** (Sizing)

Most production cache problems are **not caused by Redis** or the cache technology itself.

They are caused by poor choices around:

- TTL values
- Invalidation strategy
- Eviction policy
- Memory planning

A successful caching architecture balances:

- Freshness
- Performance
- Memory efficiency
- Operational simplicity

rather than maximizing only one of them.

---

**End of Part 5**

The next part will cover:

- **Redis Architecture**
- **Redis Data Structures**
- **Redis Persistence (RDB & AOF)**
- **Redis Replication**
- **Redis Sentinel**
- **Redis Cluster**
- **Memcached**
- **Hazelcast**
- **Apache Ignite**
- **Distributed Cache Architecture**
