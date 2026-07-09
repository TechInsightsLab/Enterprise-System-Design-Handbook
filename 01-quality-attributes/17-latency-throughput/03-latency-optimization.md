
# 12. Caching for Low Latency

> **Caching is the technique of storing frequently accessed data closer to the consumer so that future requests can be served significantly faster.**

Caching is one of the most effective techniques.

For reducing latency.

Instead of repeatedly performing expensive operations.

We reuse previously computed results.

---

# Why Caching Improves Latency

Imagine.

A product lookup.

Without cache.

```text
Client

↓

Application

↓

Database

↓

Response

↓

120 ms
```

---

With cache.

```text
Client

↓

Application

↓

Redis

↓

Response

↓

3 ms
```

The database.

Is never contacted.

---

# Real-World Analogy

Consider.

A library.

Without memory.

Every visitor.

Asks for the same book.

The librarian.

Walks to storage.

Every time.

---

With a cache.

Popular books.

Remain.

On the front desk.

Access becomes.

Almost instant.

---

# Enterprise Cache Flow

```text
Client

↓

API

↓

Redis

↓

Database

↓

Response
```

Only cache misses.

Reach the database.

---

# Cache Hit

```text
Request

↓

Cache

↓

Found

↓

Response
```

Fast.

---

# Cache Miss

```text
Request

↓

Cache

↓

Not Found

↓

Database

↓

Update Cache

↓

Response
```

Slightly slower.

Future requests.

Become faster.

---

# Cache Hit Ratio

One of the most important cache metrics.

Formula.

```text
Cache Hits

÷

Total Requests

=

Hit Ratio
```

---

# Example

```text
Total Requests

1,000,000

↓

Hits

980,000

↓

Hit Ratio

98%
```

Excellent.

---

# Benefits

Caching reduces.

- Database load
- Network traffic
- CPU usage
- Response time

It also improves.

Throughput.

Because expensive work.

Is avoided.

---

# Common Cache Locations

Examples.

```text
Browser

↓

CDN

↓

API Gateway

↓

Application

↓

Redis

↓

Database Cache
```

Multiple cache layers.

Often coexist.

---

# Cache Trade-Off

Benefits.

- Low latency
- Higher throughput
- Lower database load

Trade-offs.

- Stale data
- Cache invalidation
- Memory cost

Architects must balance.

Freshness.

And performance.

---

# Architect's Perspective

Caching reduces latency.

Only for workloads.

Where repeated access occurs.

Blindly caching everything.

Usually wastes memory.

And increases complexity.

---

# 13. Connection Pooling

> **Connection Pooling maintains a reusable pool of connections instead of creating a new connection for every request.**

Creating connections.

Is expensive.

Pooling avoids.

Repeated setup costs.

---

# Without Pooling

```text
Request

↓

Open Connection

↓

Execute Query

↓

Close Connection
```

Repeated.

Thousands of times.

---

# With Pooling

```text
Pool

↓

Reuse Existing Connection

↓

Execute Query

↓

Return Connection
```

Much faster.

---

# Database Example

Connection establishment.

May require.

```text
Authentication

↓

Network

↓

TLS

↓

Session Setup
```

Pooling eliminates.

Repeated initialization.

---

# Pool Architecture

```text
Application

↓

Connection Pool

↓

Database
```

Applications borrow.

Connections.

Instead of creating them.

---

# Benefits

Connection pooling reduces.

- Latency
- CPU usage
- Database overhead

And increases.

Throughput.

---

# Common Pools

Examples.

- HikariCP
- Apache DBCP
- c3p0

HikariCP.

Is widely used.

In Java ecosystems.

---

# Common Mistakes

### Pool Too Small

Requests wait.

For connections.

Latency increases.

---

### Pool Too Large

Database becomes overloaded.

More connections.

Do not always mean.

Higher throughput.

---

# Architect's Perspective

Connection pools should be sized based on:

- Database capacity
- CPU cores
- Expected concurrency
- Query characteristics

Not arbitrary numbers.

---

# 14. Compression

> **Compression reduces the size of data transferred across the network to decrease transmission time and bandwidth usage.**

Smaller payloads.

Travel faster.

Especially.

Across slower networks.

---

# Example

Without compression.

```text
Response

1 MB
```

---

Compressed.

```text
Response

200 KB
```

Less data.

Lower transfer time.

---

# Common Algorithms

Examples.

- Gzip
- Brotli
- Zstandard (Zstd)

Each offers different trade-offs between:

- Compression ratio
- CPU cost
- Speed

---

# Benefits

Compression improves.

- Network latency
- Bandwidth utilization
- Mobile performance

---

# Trade-Off

Compression requires.

Additional CPU.

Small payloads.

May not benefit.

Compression should be applied.

Judiciously.

---

# Architect's Perspective

Measure.

Before enabling compression.

On every response.

Not all payloads.

Benefit equally.

---

# 15. Batching

> **Batching combines multiple small operations into a single larger operation to reduce overhead and improve throughput.**

Instead of sending.

100 requests.

Send.

One request.

Containing.

100 items.

---

# Example

Without batching.

```text
100 Requests

↓

Database

↓

100 Queries
```

---

With batching.

```text
1 Request

↓

Database

↓

1 Batch Query
```

Much less overhead.

---

# Enterprise Examples

Batching is commonly used in:

- Database inserts
- Kafka producers
- Email sending
- Payment settlements
- Analytics pipelines

---

# Benefits

Batching reduces.

- Network overhead
- Serialization cost
- Database round trips

And increases throughput.

---

# Trade-Off

Larger batches.

May increase.

Individual request latency.

Finding the right batch size.

Is essential.

---

# Architect's Perspective

Batching is ideal.

For non-interactive workloads.

Real-time user requests.

May require smaller batches.

---

# 16. Pagination

Returning.

Millions of records.

Creates.

High latency.

And high memory usage.

Pagination limits.

The amount of data.

Returned.

Per request.

---

# Bad

```sql
SELECT *

FROM Orders
```

Millions of rows.

---

# Better

```text
Page

1

↓

100 Records
```

Only required data.

Is returned.

---

# Types

Common pagination strategies.

- Offset Pagination
- Cursor Pagination
- Keyset Pagination

Each has different trade-offs.

---

# Benefits

Pagination reduces.

- Database latency
- Memory usage
- Network traffic

And improves.

User experience.

---

# Architect's Perspective

Cursor or keyset pagination.

Generally scales better.

Than large OFFSET queries.

---

# 17. Asynchronous Processing

> **Asynchronous Processing allows requests to continue without waiting for long-running operations to complete.**

---

# Synchronous

```text
User

↓

Application

↓

Email

↓

SMS

↓

Payment

↓

Response
```

User waits.

For everything.

---

# Asynchronous

```text
User

↓

Application

↓

Response

↓

Queue

↓

Email

↓

SMS
```

User receives.

A faster response.

---

# Suitable Tasks

Examples.

- Emails
- Notifications
- Reports
- Image processing
- Video encoding
- Audit logs

---

# Benefits

Async processing reduces.

Perceived latency.

And improves throughput.

---

# Trade-Off

Asynchronous systems.

Introduce.

Eventual consistency.

Monitoring complexity.

Retry handling.

---

# Architect's Perspective

Not every operation.

Must complete.

Before responding.

Separate.

User-critical.

And background work.

---

# 18. Read Replicas

Databases.

Often become.

Read bottlenecks.

Read replicas.

Distribute.

Read traffic.

---

# Architecture

```text
Application

↓

Primary Database

↓

Replica 1

↓

Replica 2

↓

Replica 3
```

Reads.

Go to replicas.

Writes.

Go to primary.

---

# Benefits

- Lower latency
- Higher throughput
- Better scalability

---

# Trade-Off

Replication lag.

May introduce.

Slightly stale data.

Applications.

Must tolerate.

Eventual consistency.

---

# 19. Data Locality

> **Data Locality means processing data as close as possible to where it is stored or generated.**

Moving data.

Across regions.

Consumes time.

---

# Example

Application.

In Mumbai.

Database.

In Virginia.

Every query.

Travels thousands of kilometers.

Latency increases.

---

# Better

```text
Application

Mumbai

↓

Database

Mumbai
```

Lower network latency.

---

# Examples

- Regional databases
- Edge caches
- Local SSDs
- CDN edge locations

---

# Benefits

- Lower latency
- Reduced bandwidth
- Better customer experience

---

# 20. HTTP/2

HTTP/2 introduced.

Major improvements.

Over HTTP/1.1.

---

# Features

- Multiplexing
- Header Compression
- Server Push (limited adoption)
- Single Connection

---

# Multiplexing

Instead of.

```text
Connection 1

↓

Request

----------------

Connection 2

↓

Request
```

HTTP/2.

Allows.

```text
Single Connection

↓

Multiple Requests

Simultaneously
```

Lower latency.

---

# Benefits

- Reduced connection overhead
- Better resource utilization
- Faster page loads

---

# 21. HTTP/3

HTTP/3.

Builds on QUIC.

Instead of TCP.

---

# Advantages

- Faster connection establishment
- Reduced head-of-line blocking
- Better mobile performance
- Improved recovery from packet loss

---

# Architect's Perspective

HTTP/3 is especially valuable.

For high-latency.

Mobile networks.

---

# 22. gRPC

> **gRPC is a high-performance RPC framework that uses Protocol Buffers instead of JSON by default.**

---

# Comparison

| REST | gRPC |
|------|------|
| JSON | Protocol Buffers |
| Human Readable | Binary |
| Larger Payloads | Smaller Payloads |
| HTTP/1.1 or HTTP/2 | HTTP/2 |

---

# Benefits

gRPC provides.

- Lower serialization cost
- Smaller payloads
- Streaming support
- Lower latency

---

# Best Fit

Excellent for.

Internal service-to-service communication.

---

# 23. Content Delivery Network (CDN)

A CDN stores.

Static content.

Closer.

To users.

---

# Architecture

```text
User

↓

Nearest CDN

↓

Origin Server
```

Requests.

Travel shorter distances.

---

# Cached Content

Examples.

- Images
- CSS
- JavaScript
- Videos
- Documents

---

# Benefits

- Lower latency
- Reduced origin traffic
- Better global performance

---

# Architect's Perspective

CDNs dramatically improve.

Global application responsiveness.

Without changing application logic.

---

# 24. Edge Computing

> **Edge Computing processes requests near users instead of sending every request to centralized data centers.**

---

# Traditional

```text
User

↓

US Data Center
```

---

# Edge

```text
User

↓

Nearby Edge

↓

Only Required Data

↓

Central Cloud
```

---

# Examples

- Edge authentication
- Image resizing
- CDN functions
- IoT processing
- API gateways

---

# Benefits

- Lower latency
- Lower bandwidth usage
- Better scalability
- Regional processing

---

# Summary of Latency Optimization Techniques

| Technique | Primary Benefit |
|-----------|-----------------|
| Caching | Reduce expensive computations |
| Connection Pooling | Eliminate connection setup cost |
| Compression | Reduce payload size |
| Batching | Reduce per-request overhead |
| Pagination | Limit transferred data |
| Async Processing | Improve perceived latency |
| Read Replicas | Scale read operations |
| Data Locality | Reduce network distance |
| HTTP/2 & HTTP/3 | Improve transport efficiency |
| gRPC | Faster service communication |
| CDN | Serve content closer to users |
| Edge Computing | Process requests near users |

---

# Architect's Perspective

Reducing latency is rarely achieved through a single optimization.

High-performance systems combine multiple techniques:

- Caches avoid unnecessary work.
- Connection pools eliminate setup overhead.
- Efficient protocols reduce network costs.
- Asynchronous processing shortens user wait times.
- CDNs and edge computing minimize physical distance.

An effective architect first identifies the dominant source of latency, then applies the most appropriate optimization at that layer rather than relying on generic performance tuning.

---

**End of Part 2B**

The next part will cover:

- **Horizontal Scaling**
- **Load Balancing**
- **Partitioning**
- **Sharding**
- **Worker Pools**
- **Parallel Processing**
- **Queue-Based Architectures**
- **Backpressure**
- **Flow Control**
- **Autoscaling**
