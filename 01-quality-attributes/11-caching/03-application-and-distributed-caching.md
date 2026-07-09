
# 8. Application Cache

> **Application Cache stores frequently accessed data inside the application's memory to avoid repeated calls to external systems.**

Unlike browser or CDN caching,

application cache exists:

```
Inside

Your Application
```

---

# Architecture

```text
Client

↓

Application

↓

Application Cache

↓

Database
```

---

# Example

Configuration data.

Instead of:

```text
Every Request

↓

Database
```

Use:

```text
Load Once

↓

Memory

↓

Serve Thousands

of Requests
```

---

# What Can Be Cached?

Common examples:

- Configuration
- Feature Flags
- Country List
- Currency List
- Tax Rules
- Product Categories
- Exchange Rates

---

# Example

Without cache.

```text
Request

↓

Database

↓

Config Table
```

---

With cache.

```text
Request

↓

Memory
```

No database access.

---

# Advantages

- Extremely fast
- No network call
- Very low latency
- Simple implementation

---

# Disadvantages

Each application instance has:

Its own cache.

```text
App 1

↓

Cache A

----------------

App 2

↓

Cache B
```

Updating one cache does not update others automatically.

---

# Suitable For

- Static configuration
- Read-mostly data
- Small datasets

---

# Unsuitable For

- Shared sessions
- Frequently updated data
- Large datasets
- Cross-service communication

---

# Common Java Libraries

- Caffeine
- Guava Cache
- Ehcache

---

# Example (Spring Boot)

```java
@Cacheable("products")
public Product getProduct(Long id)
```

Spring automatically stores:

Method result.

Future calls reuse it.

---

# 9. Database Cache

> **Database Cache stores frequently accessed database pages, query results, or indexes in memory to reduce disk access.**

Every enterprise database implements some form of internal caching.

---

# Database Buffer Pool

Example.

```text
Application

↓

Database

↓

Buffer Pool

↓

Disk
```

Frequently accessed pages remain in memory.

---

# Example

Read:

Customer.

Database loads page.

Second request:

Memory.

Not disk.

---

# Benefits

- Faster queries
- Reduced disk I/O
- Better throughput

---

# Popular Databases

- PostgreSQL Shared Buffers
- MySQL InnoDB Buffer Pool
- Oracle Buffer Cache
- SQL Server Buffer Pool

---

# Query Cache

Some databases cache:

Query results.

Example.

```sql
SELECT *

FROM Product

WHERE ID = 100
```

Repeated query.

Cached result.

---

# Important Note

Modern databases increasingly favor optimized buffer caches over generic query caches because maintaining query-result caches for highly dynamic workloads can become expensive.

---

# 10. Distributed Cache

> **A Distributed Cache stores cached data in dedicated cache servers shared across multiple application instances.**

Unlike application cache,

distributed cache is:

Shared.

---

# Architecture

```text
App A

↓

Redis

↑

App B

↓

App C
```

Every application accesses:

Same cache.

---

# Benefits

- Shared cache
- Consistent data
- Horizontal scaling
- Independent deployment

---

# Example

Without Redis.

```text
App A

↓

Cache A

----------------

App B

↓

Cache B
```

Duplicate caches.

---

With Redis.

```text
App A

↓

Redis

↑

App B
```

Single source.

---

# Popular Distributed Caches

| Technology | Characteristics |
|------------|----------------|
| Redis | Rich data structures, persistence options |
| Memcached | Simple, in-memory key-value cache |
| Hazelcast | In-memory data grid |
| Apache Ignite | Distributed in-memory computing |

---

# Advantages

- Shared
- Fast
- Scalable
- High throughput

---

# Disadvantages

- Network latency
- Operational overhead
- Cache consistency
- High availability requirements

---

# Enterprise Uses

- Sessions
- Product Catalog
- Shopping Cart
- Rate Limiting
- User Profiles
- API Responses

---

# 11. Object Cache

Instead of caching:

Database rows,

cache entire business objects.

---

# Example

```json
{
 "id":101,
 "name":"Laptop",
 "price":70000,
 "brand":"ABC"
}
```

Whole object stored.

---

# Advantages

- No object reconstruction
- Less serialization overhead
- Faster responses

---

# Example

Without object cache.

```text
Database

↓

Rows

↓

Mapping

↓

Java Object
```

---

With object cache.

```text
Redis

↓

Ready Object
```

---

# Common Formats

Objects stored as:

- JSON
- Protocol Buffers
- MessagePack
- Binary serialization

Choice depends on performance, interoperability, and storage efficiency requirements.

---

# 12. Session Cache

Sessions store user-specific information.

Example.

```text
User

↓

Shopping Cart

↓

Login

↓

Preferences
```

---

# Traditional Session

```text
Application Memory
```

Problem.

Multiple servers.

Session lost.

---

# Better Architecture

```text
App A

↓

Redis

↑

App B
```

Session shared.

---

# Benefits

- Horizontal scaling
- Stateless application servers
- Better failover

---

# Example Session Data

```text
User ID

Cart

Language

Theme

Permissions
```

---

# Session Expiration

Sessions typically expire automatically.

Example:

```
30 Minutes

Inactive
```

Session removed.

---

# 13. Query Cache

Some applications cache:

Entire query results.

---

Example.

```sql
SELECT

Top Products

LIMIT 10
```

Result cached.

---

Architecture.

```text
Request

↓

Cache

↓

Query Result
```

---

# Good Candidates

- Dashboard
- Leaderboard
- Trending Products
- Popular Articles

---

# Bad Candidates

Highly dynamic queries.

Example.

```
Current Stock

Every Second
```

Stale quickly.

---

# 14. Metadata Cache

Metadata changes rarely.

Excellent cache candidate.

---

Examples.

- Table definitions
- Configuration
- Permissions
- Feature flags
- Routing information

---

Example.

Authentication.

Instead of:

```text
Database

↓

Role
```

Cache:

```
Role

Permissions
```

---

# 15. Configuration Cache

Applications constantly read:

Configuration.

---

Example.

```text
Database URL

↓

Timeout

↓

Feature Flags
```

---

Load once.

Serve from memory.

---

# Example Architecture

```text
Application Startup

↓

Configuration Server

↓

Memory Cache
```

---

# Refresh

Configuration may refresh:

- Periodically
- Via events
- On deployment
- On manual trigger

---

# 16. Near Cache

> **A Near Cache keeps frequently accessed distributed cache entries inside the application's local memory while maintaining a shared distributed cache as the source of truth.**

Combines:

Application cache

+

Redis.

---

# Architecture

```text
Application

↓

Local Cache

↓

Redis

↓

Database
```

---

Workflow

First request.

```text
Redis
```

Second request.

```text
Local Memory
```

Much faster.

---

# Benefits

- Lowest latency
- Reduced Redis traffic
- Better throughput

---

# Challenge

Invalidation.

When Redis updates,

near cache must also update.

---

# Common Platforms

- Hazelcast Near Cache
- Apache Ignite
- Infinispan
- Custom implementations using Redis notifications

---

# 17. Multi-Level Cache

Enterprise applications rarely use one cache.

Instead.

```text
L1

↓

Application Cache

↓

L2

↓

Redis

↓

L3

↓

Database
```

---

# Workflow

```text
Request

↓

L1

↓

Miss

↓

L2

↓

Miss

↓

Database

↓

Populate L2

↓

Populate L1
```

---

# Advantages

- Lowest latency
- Lowest database load
- Better scalability

---

# Drawbacks

- Increased complexity
- Multiple invalidation paths
- More monitoring required

---

# Example

Product Page.

```text
Browser

↓

CDN

↓

Application

↓

Caffeine

↓

Redis

↓

PostgreSQL
```

Several cache layers cooperate.

---

# Choosing the Right Cache

| Requirement | Cache Type |
|-------------|------------|
| Local Configuration | Application Cache |
| User Sessions | Redis |
| Frequently Read Objects | Object Cache |
| Shared State | Distributed Cache |
| Static Metadata | Metadata Cache |
| Fast Local Reads | Near Cache |
| Query Results | Query Cache |

---

# Common Mistakes

### Caching Large Objects

Consumes memory quickly.

Consider storing only frequently accessed fields or references.

---

### Caching Everything

Memory is limited.

Cache should be selective.

---

### Ignoring Cache Expiration

Old data remains indefinitely.

Eventually becomes incorrect.

---

### Local Cache in Distributed Systems

Multiple application instances.

Different cache values.

Need synchronization or invalidation.

---

# Architect's Perspective

Different caches solve different problems.

Application cache minimizes:

**Network latency.**

Distributed cache minimizes:

**Database load while enabling shared state.**

Near cache minimizes:

**Distributed cache latency.**

Object cache minimizes:

**Serialization and object construction.**

Experienced architects build **cache hierarchies**, not isolated caches.

The goal is not to cache everything.

The goal is to cache **the right data, at the right layer, for the right duration**.

---

**End of Part 3**

The next part will cover:

- **Cache-Aside (Lazy Loading)**
- **Read-Through Cache**
- **Write-Through Cache**
- **Write-Behind (Write-Back)**
- **Refresh-Ahead**
- **Cache Warming**
- **Cache Prefetching**
- **Choosing the Right Caching Pattern**
