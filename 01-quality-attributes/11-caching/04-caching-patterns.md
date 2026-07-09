
# 18. Cache-Aside Pattern (Lazy Loading)

> **Cache-Aside is the most widely used caching pattern in modern enterprise applications.**

Also known as:

```
Lazy Loading
```

The application is responsible for:

- Reading from cache
- Loading from database
- Updating the cache

The cache itself knows nothing about the database.

---

# Why Is It Called Lazy Loading?

Because data is loaded into the cache:

**Only when it is actually requested.**

Nothing is cached in advance.

---

# Architecture

```text
Application

↓

Cache

↓

Database
```

The application coordinates everything.

---

# Workflow

```text
Request

↓

Check Cache

↓

Hit?

↓

Yes → Return

↓

No

↓

Read Database

↓

Store in Cache

↓

Return
```

---

# Example

Customer requests:

```
Product #100
```

---

## First Request

```text
Client

↓

Application

↓

Redis

↓

MISS

↓

Database

↓

Product

↓

Redis

↓

Client
```

Latency is slightly higher.

Future requests become faster.

---

## Second Request

```text
Client

↓

Application

↓

Redis

↓

HIT

↓

Client
```

Database never contacted.

---

# Java Pseudocode

```java
Product product = cache.get(id);

if(product == null){
    product = database.get(id);
    cache.put(id, product);
}

return product;
```

Simple.

Highly effective.

---

# Advantages

- Easy to implement
- Cache only useful data
- Memory efficient
- Database remains source of truth
- Works well with Redis

---

# Disadvantages

- First request is slower
- Cache miss latency
- Stale data possible
- Cache consistency must be managed

---

# Best Use Cases

- Product Catalog
- User Profiles
- Country Lists
- Read-heavy APIs
- Configuration

---

# Enterprise Example

Amazon Product Page.

First request:

```
Database
```

Next:

Millions of users.

```
Redis
```

---

# 19. Read-Through Cache

> **In Read-Through caching, the application reads only from the cache. The cache automatically loads missing data from the database.**

Unlike Cache-Aside,

the application never queries the database directly.

---

# Architecture

```text
Application

↓

Cache

↓

Database
```

But now:

Cache knows how to load data.

---

# Workflow

```text
Request

↓

Cache

↓

Hit?

↓

Yes

↓

Return

↓

Miss

↓

Cache Reads Database

↓

Cache Stores Data

↓

Return
```

Application unaware.

---

# Example

```text
Application

↓

Redis

↓

Database
```

Redis (or cache provider) performs loading.

---

# Advantages

- Cleaner application code
- Centralized cache logic
- Consistent loading behavior

---

# Disadvantages

- More complex cache implementation
- Vendor/library support required
- Less flexibility

---

# Comparison

Cache Aside

```
Application

↓

Database
```

Read Through

```
Cache

↓

Database
```

---

# Enterprise Usage

Many enterprise caching frameworks provide read-through support.

However,

Cache-Aside remains more common because it offers greater flexibility and simpler operational control.

---

# 20. Write-Through Cache

> **Write-Through ensures that every write updates both the cache and the database synchronously.**

---

# Workflow

```text
Application

↓

Cache

↓

Database
```

Write completes only after both succeed.

---

# Example

Customer updates profile.

```text
Update Cache

↓

Update Database

↓

Success
```

Both remain synchronized.

---

# Advantages

- Cache always fresh
- Read-after-write consistency
- Simple reads

---

# Disadvantages

- Slower writes
- Every update touches cache
- Cache may contain rarely used data

---

# Example

```text
User Update

↓

Redis Updated

↓

Database Updated

↓

Return Success
```

---

# Good Use Cases

- User Profiles
- Preferences
- Frequently read configuration
- Small datasets

---

# 21. Write-Behind (Write-Back)

> **Write-Behind writes to the cache immediately and updates the database asynchronously later.**

Sometimes called:

```
Write Back
```

---

# Workflow

```text
Application

↓

Cache

↓

Return Success

↓

Background Update

↓

Database
```

Application returns immediately.

---

# Example

Gaming leaderboard.

```text
Score

↓

Redis

↓

Later

↓

Database
```

Fast.

---

# Advantages

- Very low write latency
- High throughput
- Reduced database load
- Batch updates possible

---

# Risks

Suppose.

Redis crashes.

Before database update.

Data may be lost unless durability mechanisms are in place.

---

# Suitable For

- Analytics
- Logging
- Metrics
- Gaming
- IoT

---

# Not Suitable For

- Banking
- Payments
- Financial systems

Strong durability required.

---

# 22. Refresh-Ahead Cache

> **Refresh-Ahead updates cached data before it expires so users rarely experience cache misses.**

---

# Normal Cache

TTL expires.

Next request.

Database queried.

---

# Refresh Ahead

Before expiration.

Background refresh.

Users continue receiving fresh data.

---

# Workflow

```text
Cache

↓

Approaching TTL

↓

Background Refresh

↓

New TTL
```

---

# Example

Weather API.

Refresh every:

```
5 Minutes
```

Users always receive fresh weather.

---

# Advantages

- High hit ratio
- Lower latency
- Fewer cache misses

---

# Drawbacks

- Background workload
- May refresh unused entries
- Requires prediction of popular keys

---

# Good Use Cases

- Exchange Rates
- Weather
- Dashboard Metrics
- Product Catalog

---

# 23. Cache Warming

> **Cache Warming proactively populates cache before user requests arrive.**

Instead of waiting for:

First request.

Populate cache:

Earlier.

---

# Example

Application starts.

Immediately load.

```text
Countries

Currencies

Tax Rules

Configuration

Top Products
```

Already cached.

---

# Startup Workflow

```text
Application

↓

Warm Cache

↓

Serve Traffic
```

---

# Advantages

- Eliminates cold start
- Faster first request
- Better user experience

---

# Drawbacks

- Longer startup
- Memory usage
- Possibly caching unused data

---

# Enterprise Example

Black Friday.

Before traffic.

Warm:

```
Top Products

Popular Categories

Discount Rules
```

Millions of users avoid cache misses.

---

# 24. Cache Prefetching

> **Cache Prefetching predicts future requests and loads data before users ask for it.**

Unlike cache warming,

prefetching happens continuously.

---

# Example

User views:

```
Product Page
```

Application predicts.

Next likely request:

```
Reviews

Recommendations

Related Products
```

Load them immediately.

---

# Workflow

```text
Current Request

↓

Prediction

↓

Background Fetch

↓

Cache
```

Future request becomes:

Cache hit.

---

# AI-Based Prefetching

Modern systems increasingly use:

- Machine Learning
- User behavior
- Recommendation models

to predict future access patterns.

---

# Benefits

- Lower latency
- Higher hit ratio
- Better user experience

---

# Challenges

Incorrect prediction wastes:

- Memory
- Network
- CPU

---

# Pattern Comparison

| Pattern | Read | Write | Best For |
|----------|------|-------|-----------|
| Cache Aside | Application | Database | General purpose |
| Read Through | Cache | Database | Framework-managed caches |
| Write Through | Cache | Cache + DB | Strong cache consistency |
| Write Behind | Cache | Async DB | High write throughput |
| Refresh Ahead | Background | Database | Frequently accessed data |
| Cache Warming | Startup | Database | Cold start prevention |
| Prefetching | Predictive | Database | User behavior optimization |

---

# Choosing the Right Pattern

## Read-Heavy Application

Example:

Product Catalog.

Use:

```
Cache Aside
```

---

## Configuration

Use:

```
Write Through
```

---

## Analytics

Use:

```
Write Behind
```

---

## Frequently Accessed APIs

Use:

```
Refresh Ahead
```

---

## Startup Optimization

Use:

```
Cache Warming
```

---

## Predictable Navigation

Use:

```
Prefetching
```

---

# Real Enterprise Examples

| Company | Typical Pattern |
|----------|-----------------|
| Amazon | Cache Aside |
| Netflix | Cache Aside + Prefetching |
| Google Maps | Prefetching |
| Facebook Feed | Cache Aside + Refresh Ahead |
| Banking | Limited Write Through for selected reference data; transactional systems still rely on the database as the system of record |

The exact implementations are proprietary, but these patterns are commonly associated with similar workloads.

---

# Common Mistakes

### Writing Only to Cache

Database never updated.

Data lost after cache restart.

---

### Refreshing Everything

Refresh only:

Frequently accessed entries.

---

### Warming Entire Database

Consumes enormous memory.

Warm only:

High-value data.

---

### Prefetching Without Measurement

Prediction accuracy matters.

Monitor effectiveness before expanding.

---

# Architect's Perspective

Caching patterns are fundamentally **consistency strategies**.

Choosing a pattern is not just about performance.

It is about answering questions such as:

- Who owns the data?
- When should the cache be updated?
- How much stale data is acceptable?
- Is write latency or read latency more important?
- Can the business tolerate asynchronous persistence?

For most enterprise applications:

- **Cache-Aside** is the default choice because of its simplicity and flexibility.
- **Write-Through** is appropriate when cached reads must immediately reflect writes.
- **Write-Behind** is valuable for high-throughput, non-critical workloads.
- **Refresh-Ahead**, **Warming**, and **Prefetching** further optimize user experience for predictable access patterns.

Architects should always start with the **simplest pattern** that satisfies business requirements before introducing more sophisticated strategies.

---

**End of Part 4**

The next part will cover:

- **TTL (Time-To-Live)**
- **Cache Expiration**
- **Cache Invalidation**
- **Eviction Policies**
- **LRU**
- **LFU**
- **FIFO**
- **Random Replacement**
- **Cache Sizing**
- **Memory Management**
