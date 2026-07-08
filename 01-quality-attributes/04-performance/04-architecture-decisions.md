
# 12. Architecture Decisions

> **Performance is determined more by architecture than by hardware.**

When performance problems appear in production, organizations often respond by:

- Increasing CPU
- Adding memory
- Scaling servers
- Upgrading databases

Sometimes this helps.

Often it simply makes expensive systems slightly less slow.

Experienced architects first ask:

- Can unnecessary work be eliminated?
- Can waiting be reduced?
- Can work execute in parallel?
- Can frequently used data be cached?
- Can requests be processed asynchronously?

Performance architecture is fundamentally about **doing less work, doing work smarter, or doing work concurrently.**

---

# Performance Decision Framework

```text
Business Objective

↓

Performance Requirement

↓

Identify Bottleneck

↓

Select Optimization Strategy

↓

Measure

↓

Validate

↓

Continuous Improvement
```

Every optimization should have measurable business value.

---

# 12.1 Caching

Caching is often the highest-impact performance optimization.

Instead of repeatedly computing or retrieving identical data:

```text
Client

↓

Cache

↓

Database
```

Frequently requested information is served directly.

---

## Business Example

E-commerce Product Page

Without cache:

```text
Customer

↓

Application

↓

Database

↓

Response
```

Every request queries the database.

---

With cache:

```text
Customer

↓

Application

↓

Redis

↓

Response
```

Database load decreases dramatically.

---

# Cache Levels

Modern systems use multiple cache layers.

```text
Browser Cache

↓

CDN

↓

API Cache

↓

Application Cache

↓

Distributed Cache

↓

Database
```

Each layer removes work from the next.

---

# Cache Strategies

## Cache Aside

Application manages the cache.

```text
Request

↓

Cache?

↓

Miss

↓

Database

↓

Update Cache
```

Most commonly used.

---

## Read Through

Cache retrieves missing data automatically.

Application only interacts with cache.

---

## Write Through

Updates both:

- Cache
- Database

simultaneously.

---

## Write Behind

Write to cache immediately.

Persist to database later.

Provides excellent write performance.

---

# Cache Decision Matrix

| Strategy | Best Use Case | Trade-off |
|----------|---------------|-----------|
| Cache Aside | General applications | Cache miss latency |
| Read Through | Transparent caching | Cache complexity |
| Write Through | Strong consistency | Slower writes |
| Write Behind | High write throughput | Eventual persistence |

---

# 12.2 Connection Pooling

Creating database connections is expensive.

Instead of:

```text
Request

↓

Create Connection

↓

Query

↓

Close Connection
```

Reuse existing connections.

---

## Architecture

```text
Application

↓

Connection Pool

↓

Database
```

---

## Benefits

- Lower latency
- Better throughput
- Reduced CPU
- Reduced database overhead

---

## Common Pools

Java Ecosystem:

- HikariCP
- Apache DBCP
- Tomcat JDBC Pool

---

# Connection Pool Sizing

Too Small:

```
Requests Wait
```

Too Large:

```
Database Overloaded
```

Proper sizing depends on:

- CPU
- Database capacity
- Query duration
- Concurrency

---

# 12.3 Query Optimization

Databases frequently dominate response time.

Poor SQL often produces larger performance problems than inefficient application code.

---

## Example

Poor query:

```sql
SELECT *
FROM Orders
WHERE CustomerName = 'John';
```

Without an index,

the database performs a full table scan.

---

Improved query:

```sql
CREATE INDEX idx_customer
ON Orders(CustomerName);
```

Response time may decrease dramatically.

---

## Query Optimization Checklist

- Appropriate indexes
- Avoid unnecessary joins
- Limit returned columns
- Filter early
- Use pagination
- Review execution plans

---

# 12.4 Pagination

Returning excessive data wastes resources.

Poor design:

```text
Load

1 Million Rows
```

---

Better design:

```text
Load

50 Rows
```

---

## Pagination Types

### Offset Pagination

```sql
LIMIT 50 OFFSET 100;
```

Simple but slower on large datasets.

---

### Keyset Pagination

```sql
WHERE Id > LastSeenId
LIMIT 50;
```

Preferred for high-performance applications.

---

# 12.5 Lazy Loading

Do not retrieve information until required.

Example:

Customer Profile

Initially:

- Name
- Email

Later:

- Purchase History
- Reviews
- Recommendations

Loading only required data reduces latency.

---

# 12.6 Eager Loading

Sometimes multiple small requests are slower than one optimized request.

Example:

Instead of:

```
Customer

↓

Orders

↓

Items

↓

Payments
```

Multiple database round trips occur.

Appropriate eager loading can reduce network latency.

---

# Lazy vs Eager Loading

| Lazy | Eager |
|------|-------|
| Lower initial cost | Fewer round trips |
| Better memory usage | Larger initial response |
| Better for optional data | Better for required data |

---

# 12.7 Asynchronous Processing

Users should not wait for unnecessary work.

Instead of:

```text
Upload

↓

Resize Image

↓

Generate Thumbnail

↓

Send Email

↓

Response
```

Return immediately.

```text
Upload

↓

Queue

↓

Response

↓

Background Worker
```

---

## Suitable Tasks

- Emails
- Notifications
- Reports
- AI inference
- PDF generation
- Image processing

---

## Benefits

- Lower response time
- Better throughput
- Higher responsiveness

---

# 12.8 Parallel Processing

Independent operations should execute simultaneously.

Sequential:

```text
User

↓

Inventory

↓

Pricing

↓

Recommendation
```

Parallel:

```text
Inventory

Pricing

Recommendation

↓

Merge

↓

Response
```

---

## Benefits

- Lower latency
- Better CPU utilization

---

# 12.9 Batching

Reduce repetitive work.

Instead of:

```
1000 Inserts
```

Perform:

```
One Batch Insert
```

---

## Advantages

- Fewer network calls
- Better database efficiency
- Higher throughput

---

# 12.10 Compression

Compression reduces transferred data.

Example:

```
2 MB JSON

↓

Compression

↓

250 KB
```

---

## Common Algorithms

- Gzip
- Brotli
- Zstandard (Zstd)

---

## Trade-off

Compression saves bandwidth but increases CPU usage.

---

# 12.11 Streaming

Large responses need not be fully generated before transmission.

Instead of:

```text
Generate Entire File

↓

Send
```

Use:

```text
Generate

↓

Stream

↓

Client Receives Progressively
```

---

## Examples

- Video
- CSV Export
- AI Responses
- Log Streaming

---

# 12.12 Algorithm Selection

No optimization exceeds choosing the correct algorithm.

Example:

```
O(n²)

↓

O(n log n)
```

may produce orders-of-magnitude improvements.

---

## Architect's Rule

Optimize algorithms before infrastructure.

---

# 12.13 Data Structures

Appropriate data structures significantly influence performance.

Examples:

| Structure | Best Use |
|-----------|----------|
| HashMap | Fast lookups |
| TreeMap | Sorted data |
| ArrayList | Sequential access |
| LinkedList | Frequent insertions |
| ConcurrentHashMap | Concurrent workloads |

---

# 12.14 Thread Pools

Creating threads repeatedly is expensive.

Applications maintain reusable thread pools.

```text
Incoming Requests

↓

Thread Pool

↓

Business Logic
```

---

## Poor Configuration

Too many threads:

- Context switching
- Memory pressure

Too few:

- Waiting requests

---

# Thread Pool Sizing

Depends on:

- CPU cores
- Blocking operations
- Concurrency
- Response time

---

# 12.15 JVM Performance

For Java systems,

architects consider:

---

## Heap Size

Too Small:

- Frequent GC

Too Large:

- Longer GC pauses

---

## Garbage Collection

Modern collectors include:

- G1 GC
- ZGC
- Shenandoah

Selection depends on latency objectives.

---

## Virtual Threads (Java 21)

Virtual Threads enable:

- Massive concurrency
- Lower thread overhead
- Simpler asynchronous programming

Ideal for:

- IO-bound applications
- High-concurrency APIs

---

## Escape Analysis

The JVM may eliminate unnecessary object allocations.

Reducing allocations improves throughput and lowers GC pressure.

---

# 12.16 Network Optimization

Performance often depends more on networking than CPU.

Optimization techniques include:

- HTTP/2
- HTTP/3
- Connection reuse
- Keep Alive
- TLS session reuse
- DNS optimization

---

# 12.17 CDN

Global applications reduce latency by serving static content closer to users.

```text
User (Singapore)

↓

Singapore Edge

↓

Image
```

instead of:

```text
Singapore

↓

US Server
```

---

# 12.18 Read Replicas

Separate read workloads.

```text
Application

↓

Primary

↓

Replica 1

Replica 2
```

Reads scale independently.

---

# 12.19 Materialized Views

Expensive queries may be precomputed.

Instead of repeatedly joining multiple tables,

retrieve:

```
Precomputed Results
```

Useful for dashboards and reporting.

---

# 12.20 Performance Decision Matrix

| Performance Problem | Recommended Decision |
|---------------------|----------------------|
| Slow Reads | Cache |
| Slow SQL | Query Optimization |
| Database Contention | Read Replicas |
| Long-running Tasks | Async Processing |
| High Network Cost | Compression |
| Large Responses | Pagination |
| Independent Tasks | Parallel Processing |
| Repeated Connections | Connection Pooling |
| Large File Downloads | Streaming |
| High CPU | Better Algorithm |
| Global Latency | CDN |
| High Concurrency | Virtual Threads / Efficient Thread Pools |

---

# Architecture Decision Summary

| Decision | Primary Benefit | Common Trade-off |
|----------|-----------------|------------------|
| Cache | Lower latency | Consistency |
| Connection Pool | Faster database access | Pool tuning |
| Query Optimization | Better SQL performance | Development effort |
| Pagination | Lower memory usage | More requests |
| Async Processing | Faster responses | Operational complexity |
| Parallel Processing | Lower latency | Synchronization |
| Batching | Higher throughput | Delayed processing |
| Compression | Lower bandwidth | CPU overhead |
| Streaming | Faster perceived performance | Implementation complexity |
| CDN | Lower global latency | Cache invalidation |
| Read Replicas | Read scalability | Replication lag |
| Materialized Views | Fast analytics | Refresh complexity |

---

# Architect's Perspective

Performance is rarely improved through a single optimization.

Instead, high-performance systems combine multiple architectural techniques.

For example, a modern enterprise platform may use:

- Redis for frequently accessed data
- Connection pooling for efficient database access
- Optimized SQL with proper indexing
- Asynchronous processing for long-running workflows
- Parallel execution of independent service calls
- Compression for network efficiency
- CDN for global content delivery
- Virtual Threads for high-concurrency Java workloads
- Read replicas for query scalability
- Materialized views for reporting

Each decision targets a specific bottleneck.

The objective is not to apply every optimization, but to apply the right optimization where it delivers measurable business value.

---


**End of Part 4**

The next section will cover **Performance Mechanisms**, including:

- JVM internals and tuning
- Spring Boot performance techniques
- Redis optimization
- Kafka producer/consumer tuning
- Database engine tuning
- Kubernetes resource optimization
- AWS, Azure, and GCP performance services
- Profiling tools
- Benchmarking frameworks
- Performance implementation decision matrix
