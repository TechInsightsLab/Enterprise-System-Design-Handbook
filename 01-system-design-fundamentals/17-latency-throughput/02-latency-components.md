
# 1. Response Time vs Latency

Many engineers use these terms.

Interchangeably.

Although related.

They are not identical.

Understanding the difference.

Is important.

For designing high-performance systems.

---

# Response Time

> **Response Time is the total time experienced by the client from sending a request until receiving the complete response.**

It represents.

The complete user experience.

Example.

```text
Client

↓

Request Sent

↓

Server Processing

↓

Response Received
```

Everything is included.

---

# Components of Response Time

```text
Client

↓

DNS Resolution

↓

TCP Connection

↓

TLS Handshake

↓

Network

↓

Load Balancer

↓

Application

↓

Database

↓

Cache

↓

Network

↓

Client
```

Response time is the sum of every delay.

---

# Latency

Latency refers to.

The delay introduced.

At a specific stage.

Examples.

```text
Database Latency

40 ms

-------------------

Network Latency

15 ms

-------------------

Cache Latency

2 ms
```

Each component contributes.

To total response time.

---

# Example

Customer requests.

```
GET /orders
```

Measurements.

```text
DNS

10 ms

↓

Network

20 ms

↓

Load Balancer

3 ms

↓

Application

80 ms

↓

Database

40 ms

↓

Serialization

12 ms

↓

Return Network

20 ms
```

Total Response Time.

```
185 ms
```

---

# Visualization

```text
Latency

↓

Individual Delay

----------------------------

Response Time

↓

Entire Journey
```

---

# Business Perspective

Customers.

Do not care.

Whether delay occurs.

In DNS.

Database.

Or application.

They experience.

Only.

Response Time.

Architects.

Must optimize.

Every component.

---

# Architect's Perspective

Latency analysis helps identify.

Individual bottlenecks.

Response time reflects.

Overall customer experience.

Both should be measured.

---

# 2. Bandwidth vs Throughput

Another commonly confused pair.

Bandwidth.

And throughput.

They are related.

But fundamentally different.

---

# What is Bandwidth?

> **Bandwidth is the theoretical maximum amount of data that a communication channel can transfer over a period of time.**

Bandwidth.

Represents capacity.

Not actual performance.

---

# Example

Internet connection.

```
1 Gbps
```

Maximum possible.

Bandwidth.

---

# What is Throughput?

Throughput.

Represents.

Actual useful data transferred.

Example.

```
650 Mbps
```

Actual throughput.

Bandwidth.

Was.

```
1 Gbps
```

Difference caused by.

- Protocol overhead
- Packet loss
- Retransmissions
- Congestion
- Encryption
- Latency

---

# Highway Analogy

Bandwidth.

```text
Number of Lanes
```

Throughput.

```text
Cars Successfully Passing
```

A six-lane highway.

May still have.

Low throughput.

During traffic jams.

---

# Pipe Analogy

Bandwidth.

Diameter.

Of the pipe.

Throughput.

Actual water flowing.

Large pipes.

Do not guarantee.

High throughput.

If valves are partially closed.

---

# Example

Cloud Storage.

Bandwidth.

```
10 Gbps
```

Measured throughput.

```
3.8 Gbps
```

The remaining capacity.

May be lost.

To congestion.

Protocol overhead.

Or storage bottlenecks.

---

# Comparison

| Bandwidth | Throughput |
|-----------|------------|
| Maximum capacity | Actual useful work |
| Theoretical | Practical |
| Usually fixed | Varies continuously |
| Larger is better | Higher is better |

---

# Architect's Perspective

Buying larger bandwidth.

Does not automatically improve throughput.

System bottlenecks.

Must also be removed.

---

# 3. Network Latency

> **Network Latency is the time required for data to travel between two endpoints across a network.**

Every distributed system.

Pays.

Network cost.

---

# Request Journey

```text
Client

↓

ISP

↓

Internet

↓

CDN

↓

Load Balancer

↓

Application

↓

Database
```

Every hop.

Adds latency.

---

# Sources

Network latency includes.

- Physical distance
- Routers
- Firewalls
- Load Balancers
- Congestion
- Packet retransmission

---

# Physical Distance

Signals.

Cannot travel instantly.

Approximate speed.

Inside fiber.

```
~200,000 km/sec
```

Longer distance.

Higher latency.

---

# Example

User.

In India.

Calling server.

In US.

Round trip.

May exceed.

```
250 ms
```

Without application processing.

---

# Cross Region Example

```text
India

↓

Singapore

↓

Frankfurt

↓

Virginia
```

Each region.

Adds latency.

---

# Measuring Network Latency

Common tools.

- ping
- traceroute
- mtr

Cloud providers also expose.

Network metrics.

---

# Optimization

Architects reduce network latency using.

- CDN
- Edge Computing
- Regional deployments
- Load balancing
- Data locality
- HTTP/2
- HTTP/3

These topics are covered later.

---

# Architect's Perspective

For globally distributed systems.

Network latency.

Often becomes.

The largest component.

Of total response time.

---

# 4. DNS Resolution

Before a request reaches.

The application.

The client must determine.

The server's IP address.

---

# DNS Flow

```text
Browser

↓

Local Cache

↓

Recursive Resolver

↓

Authoritative DNS

↓

IP Address
```

Only then.

Can communication begin.

---

# Example

```
api.company.com

↓

34.201.18.45
```

DNS translates.

Names.

Into addresses.

---

# Latency

Typical DNS lookup.

```text
10-80 ms
```

Depending on.

Caching.

Provider.

Location.

---

# DNS Caching

Caching reduces.

Repeated lookups.

```text
First Request

40 ms

↓

Later Requests

1 ms
```

Huge improvement.

---

# Optimization

- Local DNS cache
- CDN DNS
- Anycast DNS
- Low TTL only when necessary

---

# Architect's Perspective

DNS latency.

Usually appears.

Only once.

Per connection.

But can become noticeable.

For mobile clients.

---

# 5. TLS Handshake

Modern applications.

Use HTTPS.

Before exchanging data.

The client.

Must establish.

A secure connection.

---

# TLS Flow

```text
Client

↓

TCP

↓

TLS Handshake

↓

Encrypted Connection

↓

HTTP Request
```

The handshake.

Introduces additional latency.

---

# Typical Cost

TLS handshake.

May require.

```
20-100 ms
```

Depending on.

Distance.

Certificates.

Protocol version.

---

# Optimization

Modern improvements.

Include.

- TLS 1.3
- Session Resumption
- HTTP/2
- HTTP/3 (QUIC)

Reducing handshake overhead.

---

# Architect's Perspective

Short-lived connections.

Spend significant time.

Performing handshakes.

Connection reuse.

Becomes important.

---

# 6. Serialization & Deserialization

Distributed systems.

Exchange data.

As bytes.

Objects.

Must therefore be converted.

---

# Serialization

```text
Java Object

↓

JSON

↓

Network
```

---

# Deserialization

```text
JSON

↓

Java Object
```

Every request.

Pays this cost.

---

# Common Formats

- JSON
- XML
- Protocol Buffers
- Avro
- MessagePack

Each balances.

Readability.

Size.

Performance.

---

# Performance Comparison

Generally.

```text
Protocol Buffers

↓

Fast

↓

Compact

-------------------

JSON

↓

Readable

↓

Larger

-------------------

XML

↓

Largest

↓

Slowest
```

Actual performance depends on workload and implementation.

---

# Optimization

Reduce serialization cost by.

- Smaller payloads
- Compression (when appropriate)
- Efficient binary formats
- Avoid unnecessary object creation

---

# Architect's Perspective

Serialization overhead.

Becomes significant.

In microservice architectures.

Where data crosses.

Many service boundaries.

---

# 7. Database Latency

Databases.

Are among the most common.

Performance bottlenecks.

---

# Query Flow

```text
Application

↓

Connection Pool

↓

Database

↓

Disk

↓

Memory

↓

Return Result
```

Every stage.

Adds latency.

---

# Causes

Database latency may result from.

- Missing indexes
- Table scans
- Lock contention
- Network delays
- Disk I/O
- Slow joins
- Large result sets

---

# Example

Simple lookup.

```
3 ms
```

Complex join.

```
450 ms
```

Good schema design.

Often matters more.

Than faster hardware.

---

# Optimization

Improve database latency through.

- Proper indexing
- Query optimization
- Read replicas
- Connection pooling
- Caching
- Partitioning
- Sharding (when appropriate)

---

# Architect's Perspective

Many application performance problems.

Are actually.

Database problems.

Always measure.

Before optimizing code.

---

# 8. Disk I/O Latency

Storage.

Is much slower.

Than memory.

Applications waiting.

For disk.

Often become.

Latency bound.

---

# Relative Speed

Approximate order.

```text
CPU Cache

↓

RAM

↓

NVMe SSD

↓

SSD

↓

HDD

↓

Remote Storage
```

Every level.

Introduces additional latency.

---

# Common Causes

- Large file reads
- Random I/O
- Log writes
- Database flushes
- Checkpoint operations

---

# Optimization

Reduce disk latency by.

- Caching
- SSD/NVMe storage
- Sequential I/O
- Buffer pools
- Asynchronous writes
- Efficient indexing

---

# 9. Garbage Collection (GC) Latency

Managed runtimes.

Such as Java.

Automatically reclaim memory.

During garbage collection.

Application threads.

May pause.

---

# Example

```text
Application Running

↓

GC Starts

↓

Pause

↓

Application Resumes
```

Long pauses.

Increase latency.

---

# Causes

- Memory pressure
- Large heaps
- Excessive object allocation
- Inefficient GC configuration

---

# Optimization

Architects reduce GC latency by.

- Choosing appropriate collectors
- Reducing object creation
- Right-sizing heap memory
- Monitoring GC metrics

---

# 10. Lock Contention

Concurrency improves throughput.

But excessive locking.

Can increase latency.

---

# Example

```text
Thread 1

↓

Lock

↓

Critical Section

↓

Unlock

-----------------------

Thread 2

↓

Waiting...
```

Waiting time.

Becomes latency.

---

# Causes

- Large synchronized blocks
- Hot shared resources
- Database row locks
- Distributed locks

---

# Optimization

- Reduce lock scope
- Partition workloads
- Use lock-free algorithms where appropriate
- Minimize shared mutable state

---

# 11. External API Latency

Modern systems.

Frequently depend on.

Third-party services.

Examples.

- Payment gateways
- SMS providers
- Identity providers
- Maps
- Shipping services

These dependencies.

Often dominate.

End-to-end latency.

---

# Example

```text
Application

↓

Payment Gateway

↓

Bank

↓

Authorization

↓

Response
```

Your application.

Cannot control.

Every step.

---

# Common Challenges

- Variable response times
- Network instability
- Rate limiting
- Regional outages
- Provider throttling

---

# Optimization

Mitigation strategies include.

- Timeouts
- Circuit breakers
- Retries with backoff
- Caching
- Asynchronous processing
- Fallback mechanisms

---

# Summary of Latency Sources

| Source | Typical Impact |
|---------|----------------|
| Network | Physical distance and routing delays |
| DNS | Name resolution overhead |
| TLS | Secure connection establishment |
| Serialization | Object ↔ wire format conversion |
| Database | Query execution and storage access |
| Disk I/O | Persistent storage delays |
| Garbage Collection | Runtime pause time |
| Lock Contention | Waiting for shared resources |
| External APIs | Third-party dependency latency |

---

# Architect's Perspective

When users complain that **"the system is slow,"** the application code is only one possible cause.

An experienced architect decomposes the end-to-end request into individual latency components, measures each stage, identifies the true bottleneck, and optimizes the highest-impact contributor first.

Performance engineering begins with **measurement**, not assumptions.

---

**End of Part 2A**

The next part will cover:

- **Caching Strategies for Low Latency**
- **Connection Pooling**
- **Compression**
- **Batching**
- **Pagination**
- **Asynchronous Processing**
- **Read Replicas**
- **Data Locality**
- **HTTP/2 & HTTP/3**
- **gRPC**
- **CDN**
- **Edge Computing**
