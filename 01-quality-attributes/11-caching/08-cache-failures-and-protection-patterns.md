
# 54. Cache Stampede (Dogpile Effect)

> **A Cache Stampede occurs when a popular cache entry expires and many requests simultaneously attempt to rebuild the same data, overwhelming the backend system.**

This is one of the most common production problems in large-scale systems.

---

# Why Does It Happen?

Imagine:

```
Product #100
```

is viewed:

```
100,000 Times/Minute
```

The cache entry expires.

Suddenly,

all requests experience:

```
Cache Miss
```

Every request goes to:

```
Database
```

Instead of one request,

the database now receives:

```
100,000 Queries
```

Within seconds.

---

# Architecture

```text
Before Expiration

Application

↓

Redis

↓

Database

(Cache Hit)

-----------------------

After Expiration

Application

↓

Redis (MISS)

↓

Database

↑ ↑ ↑ ↑ ↑ ↑ ↑

Thousands of Requests
```

Database overloads.

---

# Timeline

```text
12:00

↓

Cache Valid

↓

12:30

↓

TTL Expired

↓

12:30:01

↓

100,000 Requests

↓

Database Crash
```

---

# Business Impact

Possible consequences:

- Database overload
- Increased latency
- API timeouts
- Cascading failures
- Customer complaints

---

# Prevention Techniques

- Distributed Locking
- Request Coalescing (SingleFlight)
- Refresh Ahead
- Cache Warming
- Randomized TTL
- Stale-While-Revalidate

---

# Solution 1 — Distributed Lock

Allow:

Only one request.

To rebuild cache.

---

Workflow

```text
100 Requests

↓

Acquire Lock

↓

1 Winner

↓

Database

↓

Update Cache

↓

Release Lock

↓

Others Read Cache
```

Database receives:

One query.

Not one hundred thousand.

---

# Solution 2 — Stale While Revalidate

Instead of:

Returning miss.

Serve:

Slightly stale data.

Refresh in background.

---

Workflow

```text
Cache Expired

↓

Serve Old Value

↓

Background Refresh

↓

Update Cache
```

Users remain happy.

Database protected.

---

# Solution 3 — Random TTL

Bad.

Every key expires:

```
12:00
```

Good.

```
12:01

12:03

12:07

12:10
```

Expiration distributed.

No traffic spike.

---

# Randomized TTL Example

Instead of:

```
TTL = 1 Hour
```

Use.

```
TTL =

1 Hour

+

Random(0-10 Minutes)
```

Large systems widely use this strategy.

---

# Solution 4 — Refresh Ahead

Popular entries refreshed.

Before expiration.

Users rarely experience cache misses.

---

# 55. Cache Avalanche

> **A Cache Avalanche occurs when a large number of cache entries expire simultaneously, causing a sudden surge of requests to backend systems.**

Unlike Stampede:

Many keys expire.

Not one.

---

# Example

Imagine:

```
1 Million Products
```

All cached.

All configured:

```
TTL

=

60 Minutes
```

Exactly.

---

# Timeline

```text
10:00

↓

All Cached

↓

11:00

↓

All Expire

↓

Database Flood
```

---

# Difference

Stampede:

```
One Hot Key
```

Avalanche:

```
Thousands

or

Millions

of Keys
```

---

# Prevention

- Random TTL
- Multi-Level Cache
- Refresh Ahead
- Cache Warming
- Gradual expiration

---

# Enterprise Example

Black Friday.

Entire product catalog expires.

Millions of users.

Database overloaded.

---

# 56. Cache Penetration

> **Cache Penetration occurs when requests repeatedly ask for data that does not exist in either the cache or the database.**

Every request becomes:

```
Cache Miss

↓

Database

↓

Nothing Found
```

---

# Example

User requests.

```
Product

999999999
```

Product doesn't exist.

Every request:

Queries database.

---

# Attack Scenario

Malicious bot.

Generates.

```
Random IDs
```

Millions.

Database overwhelmed.

---

# Solution 1 — Cache Null Values

Database returns:

Not Found.

Store:

```
NULL
```

In cache.

Future requests:

No database query.

---

# Example

```text
Key

↓

NULL

TTL

↓

5 Minutes
```

---

# Advantages

Simple.

Highly effective.

---

# Drawback

Temporary memory usage.

---

# Solution 2 — Bloom Filter

Bloom Filter determines.

Likely exists?

Before accessing:

Cache.

---

Workflow

```text
Request

↓

Bloom Filter

↓

No

↓

Reject

-----------------

Yes

↓

Redis

↓

Database
```

Many invalid requests stopped immediately.

---

# 57. Bloom Filter

> **A Bloom Filter is a probabilistic data structure that efficiently tests whether an element may exist in a set.**

Important.

Bloom Filter.

Can answer.

```
Definitely Not
```

or.

```
Possibly Exists
```

Never.

```
Definitely Exists
```

---

# Characteristics

- Extremely memory efficient
- Very fast
- No false negatives
- Possible false positives

---

# Example

Question.

```
Customer

100
```

Bloom Filter.

```
No
```

Guaranteed.

Customer absent.

Database never queried.

---

# Uses

- Cache penetration prevention
- URL lookup
- Duplicate detection
- Distributed databases

---

# Enterprise Example

Large e-commerce.

```
100 Million Products
```

Bloom Filter prevents invalid IDs reaching database.

---

# 58. Hot Keys

> **A Hot Key is a cache entry that receives disproportionately high traffic compared to other keys.**

Example.

```
Today's Deal
```

Receives:

```
2 Million Requests

per Minute
```

Single Redis node overloaded.

---

# Architecture

```text
Redis Node

↓

Today's Deal

↓

2 Million Reads
```

One node becomes hotspot.

---

# Causes

- Viral content
- Flash sales
- Breaking news
- Live sports
- Celebrity posts

---

# Problems

- CPU saturation
- Network bottleneck
- Increased latency
- Node imbalance

---

# Solutions

- Local cache
- Near cache
- Replication
- Key replication (where appropriate)
- CDN
- Load balancing

---

# Hot Key Replication

Instead of:

```
product:100
```

Create.

```
product:100:1

product:100:2

product:100:3
```

Reads distributed.

Writes become more complex because all replicas must remain synchronized.

---

# 59. Cold Cache

Cold cache means:

Cache empty.

---

Example.

Application restart.

Redis restart.

Deployment.

---

Workflow

```text
Application Starts

↓

Empty Cache

↓

Every Request

↓

Database
```

Database spike.

---

# Prevention

- Cache Warming
- Refresh Ahead
- Backup restoration (where supported)
- Gradual traffic ramp-up

---

# Enterprise Example

Deployment.

Warm:

- Top Products
- Categories
- Configuration
- Exchange Rates

Before accepting production traffic.

---

# 60. Cache Breakdown

Sometimes called:

```
Hot Key Expiration
```

---

One popular key.

Expires.

Millions of requests.

Database overload.

Very similar to:

Stampede.

Some communities use these terms interchangeably, while others reserve "cache breakdown" for a single extremely hot key.

---

# Prevention

- Distributed lock
- Never expire hottest keys (use caution)
- Refresh Ahead
- Stale While Revalidate

---

# 61. Distributed Locking

Distributed lock ensures:

Only one application performs an expensive operation.

---

# Example

```text
100 Requests

↓

Acquire Lock

↓

Winner

↓

Database

↓

Redis

↓

Unlock
```

Others wait.

---

# Redis Lock

Typical workflow.

```text
SET lock:key value NX EX 30
```

Meaning.

```
Only if absent.

Expire after 30 Seconds.
```

This prevents permanent deadlocks if the process holding the lock crashes.

---

# Use Cases

- Cache rebuild
- Inventory updates
- Scheduled jobs
- Leader election
- Payment processing coordination

---

# Considerations

Distributed locking requires careful implementation to avoid deadlocks, orphaned locks, and split-brain scenarios.

---

# 62. Request Coalescing (SingleFlight)

> **Request Coalescing ensures that multiple identical requests share a single backend operation instead of executing duplicate work.**

Popular name.

```
SingleFlight
```

---

# Example

100 users request:

```
Product

100
```

Simultaneously.

---

Without SingleFlight.

```text
100 Database Queries
```

---

With SingleFlight.

```text
100 Requests

↓

One Database Query

↓

Shared Response

↓

100 Users
```

Huge efficiency.

---

# Difference from Distributed Lock

Distributed Lock.

Protects.

Shared resources.

Across applications.

---

SingleFlight.

Deduplicates.

Identical in-flight requests.

Usually inside one application instance.

Many systems combine both approaches in distributed environments.

---

# Production Scenario

Product cache expires.

Thousands of requests arrive.

SingleFlight.

One request.

Database.

Others wait.

---

# Failure Recovery

If cache fails.

Application should:

- Continue serving requests
- Degrade gracefully
- Avoid overwhelming database
- Trigger alerts

Never assume cache availability.

---

# Monitoring Cache Failures

Monitor:

- Cache hit ratio
- Miss ratio
- Stampede frequency
- Lock contention
- Evictions
- Expired keys
- Backend query rate
- Cache latency

These metrics provide early warning before customer impact.

---

# Failure Comparison

| Problem | Cause | Primary Solution |
|----------|-------|------------------|
| Cache Stampede | One hot key expires | Distributed Lock / SingleFlight |
| Cache Avalanche | Many keys expire | Random TTL |
| Cache Penetration | Invalid keys | Bloom Filter / Cache Null |
| Hot Key | Uneven traffic | Replication / Near Cache |
| Cold Cache | Empty cache | Cache Warming |
| Cache Breakdown | Hot key expiration | Refresh Ahead / Locking |

---

# Common Mistakes

### Same TTL Everywhere

Millions of keys expire simultaneously.

Always introduce expiration jitter for large datasets.

---

### No Protection Against Invalid Requests

Bots can overload databases using nonexistent IDs.

Use Bloom Filters or cache negative lookups.

---

### Assuming Cache Never Fails

Redis outages happen.

Applications must degrade gracefully.

---

### Ignoring Hot Keys

One key can overload an entire Redis node while others remain mostly idle.

Monitor access distribution.

---

### No Request Deduplication

Hundreds of identical requests perform the same expensive computation.

Use SingleFlight or equivalent request coalescing.

---

# Architect's Perspective

The most difficult cache problems usually appear **only at scale**.

A cache that performs well with:

```
100 Users
```

may fail dramatically with:

```
10 Million Users
```

Enterprise architects design not only for:

- Cache hits

but also for:

- Cache misses
- Cache failures
- Traffic spikes
- Bot attacks
- Popular content
- Regional outages

A production-ready caching architecture assumes that:

- Keys will expire.
- Redis may restart.
- Traffic will spike unexpectedly.
- Users will request the same data simultaneously.

Designing for these scenarios is what separates an enterprise caching architecture from a simple in-memory optimization.

---

**End of Part 8**

The next part will cover:

- **Caching in Microservices**
- **API Gateway Caching**
- **Database Query Caching**
- **GraphQL Caching**
- **CDN Architecture**
- **Cloud Cache Services (AWS, Azure, GCP)**
- **Event-Driven Cache Invalidation**
- **Multi-Region Caching**
- **Enterprise Caching Architectures**
