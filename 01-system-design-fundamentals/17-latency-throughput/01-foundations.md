
# Latency & Throughput

> **Part I – System Design Foundations**

---

# Latency & Throughput

| Attribute | Value |
|-----------|--------|
| **Version** | 2.0 |
| **Part** | Part I – System Design Foundations |
| **Chapter** | 17 |
| **Reading Time** | ~500–650 Minutes |
| **Difficulty** | Advanced |
| **Last Updated** | July 2026 |

---

> [!NOTE]
> **Most systems do not fail because they stop working. They fail because they become too slow under increasing load. Latency and Throughput determine whether a system can continue delivering a good customer experience as demand grows.**

---

# Why This Chapter Exists

Every user expects.

Applications to be.

Fast.

Responsive.

Reliable.

Whether it is:

- Ordering food
- Booking flights
- Paying online
- Streaming videos
- Trading stocks
- Searching products

Performance directly affects customer satisfaction.

A delay of a few hundred milliseconds may seem insignificant.

At enterprise scale.

It can translate into millions of dollars in lost revenue.

---

# Business Story

Imagine two online shopping platforms.

---

## Company A

Average checkout response.

```
4 Seconds
```

Customers become impatient.

Many abandon their carts.

Sales decrease.

Marketing spends more money.

To acquire new customers.

---

## Company B

Average checkout response.

```
250 ms
```

Customers complete purchases quickly.

Revenue increases.

Customer satisfaction improves.

The business grows.

---

The difference.

Was not more features.

It was lower latency.

---

# What is Latency?

> **Latency is the time taken for a request or operation to travel from its starting point to completion.**

Latency answers the question:

> **"How long does one operation take?"**

Examples.

- API response time
- Database query time
- Kafka message delivery time
- Cache lookup time
- File upload time

Latency is measured.

Per request.

---

# What is Throughput?

> **Throughput is the amount of useful work a system can complete during a specific period of time.**

Throughput answers the question:

> **"How much work can the system perform?"**

Examples.

- Requests per second (RPS)
- Transactions per second (TPS)
- Messages per second
- Orders per minute
- Images processed per hour

Throughput measures capacity.

Not speed.

---

# Simple Analogy

Imagine.

A highway.

Latency.

Represents.

How long.

One car takes.

To travel.

From one city.

To another.

---

Throughput.

Represents.

How many cars.

Can travel.

On the highway.

Every hour.

---

A wider highway.

May carry.

More cars.

Without making.

Each individual car.

Travel faster.

---

# Restaurant Analogy

Consider.

A restaurant.

Latency.

```
Customer

↓

Order

↓

Food Served

↓

15 Minutes
```

Throughput.

```
Customers Served

↓

300

Per Hour
```

These are related.

But different.

---

# Why Architects Care

Enterprise systems.

Must optimize.

Both.

```text
Fast Individual Requests

+

Large Number of Requests
```

Optimizing only one.

Usually creates.

An unbalanced system.

---

# Relationship

Many engineers assume.

Reducing latency.

Automatically increases throughput.

This is not always true.

Likewise.

Increasing throughput.

May increase latency.

These quality attributes influence each other.

But are not identical.

---

# Visualizing the Difference

```text
Latency

↓

Time Per Request

----------------------------

Throughput

↓

Requests Per Second
```

---

# Real-World Examples

## ATM

Latency.

```
Withdraw Cash

↓

5 Seconds
```

Throughput.

```
Customers

↓

20

Per Hour
```

---

## Netflix

Latency.

```
Start Video

↓

300 ms
```

Throughput.

```
Millions of Streams

Simultaneously
```

---

## Payment Gateway

Latency.

```
Payment Authorization

↓

180 ms
```

Throughput.

```
50,000 TPS
```

---

# Units of Measurement

Latency.

Typically measured in.

- Nanoseconds (ns)
- Microseconds (µs)
- Milliseconds (ms)
- Seconds (s)

Most enterprise APIs target.

Milliseconds.

---

Throughput.

Typically measured in.

- Requests/sec
- Transactions/sec
- Events/sec
- Messages/sec
- MB/sec
- GB/sec

The unit depends on the workload.

---

# Why Both Matter

Imagine.

A payment gateway.

Latency.

```
80 ms
```

Excellent.

But.

Throughput.

```
10 TPS
```

During a festival.

The system collapses.

---

Another system.

Throughput.

```
100,000 TPS
```

Excellent.

Latency.

```
8 Seconds
```

Customers abandon payments.

Neither system.

Meets business expectations.

---

# Latency vs Throughput

| Latency | Throughput |
|----------|------------|
| Time taken by one request | Amount of work completed |
| Measured per operation | Measured over time |
| Lower is generally better | Higher is generally better |
| Units: ms, µs, s | Units: RPS, TPS, MB/s |

---

# Business Impact

High latency causes.

- Poor user experience
- Cart abandonment
- Lower conversion rates
- SLA violations
- Reduced customer satisfaction

---

Low throughput causes.

- Long queues
- Request timeouts
- System overload
- Revenue loss during peak traffic
- Poor scalability

---

# Latency and Throughput Together

An enterprise system should aim for.

```text
Low Latency

+

High Throughput

+

Predictable Performance
```

Not merely.

Fast averages.

---

# Common Misconceptions

### Misconception 1

Higher CPU.

Means higher throughput.

False.

The bottleneck may be:

- Database
- Network
- Disk
- Lock contention

---

### Misconception 2

Adding servers.

Always reduces latency.

False.

Additional network hops.

May actually increase latency.

---

### Misconception 3

Caching solves every performance problem.

False.

Caching helps.

Specific workloads.

Not all workloads.

---

### Misconception 4

Average latency tells the whole story.

False.

Customers experience.

Tail latency.

Which is often far more important.

This will be covered later.

---

# Relationship with Previous Chapters

Latency & Throughput build upon multiple earlier topics.

| Previous Chapter | Contribution |
|------------------|-------------|
| Scalability | Handle more traffic |
| Performance | General optimization |
| Caching | Reduce latency |
| Messaging | Increase throughput |
| API Design | Efficient communication |
| Security | Performance trade-offs |
| Observability | Measure latency & throughput |
| Maintainability | Sustain optimizations over time |

This chapter connects these concepts into a performance-focused architectural perspective.

---

# Table of Contents

## Part 1 – Foundations

- What is Latency?
- What is Throughput?
- Response Time vs Latency
- Bandwidth vs Throughput
- Why They Matter
- Business Impact

---

## Part 2 – Sources of Latency

- Network Latency
- DNS Resolution
- TLS Handshake
- Serialization
- Database Queries
- Disk I/O
- Garbage Collection
- Lock Contention
- External APIs

---

## Part 3 – Latency Optimization

- Caching
- Connection Pooling
- Compression
- Batching
- Pagination
- Async Processing
- Read Replicas
- Data Locality
- HTTP/2
- HTTP/3
- gRPC
- CDN
- Edge Computing

---

## Part 4 – Throughput Optimization

- Horizontal Scaling
- Load Balancing
- Partitioning
- Sharding
- Worker Pools
- Parallel Processing
- Queue-Based Architectures
- Autoscaling
- Backpressure
- Flow Control

---

## Part 5 – Performance Measurement

- Average vs Median
- Percentiles (P50, P95, P99, P99.9)
- Tail Latency
- Little's Law
- Queueing Theory Basics
- Capacity Planning
- Benchmarking
- Load Testing
- Stress Testing
- Spike Testing
- Soak Testing

---

## Part 6 – Enterprise Practices

- Performance Budgets
- Performance Anti-Patterns
- Enterprise Case Study
- Architecture Review Checklist
- Interview Questions
- Revision Cheat Sheet

---

# Learning Objectives

After completing this chapter you should be able to:

- Distinguish latency from throughput.
- Identify latency bottlenecks.
- Design low-latency systems.
- Design high-throughput systems.
- Measure performance correctly.
- Interpret percentile metrics.
- Apply architectural optimization techniques.
- Balance latency, throughput, cost, and reliability.
- Design systems for sustained peak loads.

---

# Prerequisites

Readers should already understand:

- High Availability
- Reliability
- Scalability
- Performance
- Fault Tolerance
- Resilience
- Consistency
- Distributed Transactions
- Concurrency
- Data Management
- Caching
- Messaging
- API Design
- Security
- Observability
- Maintainability

---

# Next Chapters

After this chapter, recommended topics include:

- Distributed Systems Fundamentals
- Time & Clocks
- CAP Theorem
- Consensus Algorithms
- Data Replication
- Distributed Coordination
- Service Discovery

---

# Architect's Perspective

Performance is one of the few architectural qualities that customers experience directly.

Customers rarely notice elegant architecture.

They immediately notice:

- Slow pages.
- Slow payments.
- Slow searches.
- Slow APIs.

Architects must therefore design systems that minimize latency while maximizing throughput under expected and unexpected workloads.

This requires understanding not only how individual components perform, but how queues, networks, databases, caches, protocols, and distributed systems interact under load.

The objective is not simply to build **fast systems**.

It is to build systems that remain **predictably fast** as demand grows.

---

**End of Part 1**

The next part will cover:

- **Response Time vs Latency**
- **Bandwidth vs Throughput**
- **Network Latency**
- **DNS Resolution**
- **TLS Handshake**
- **Serialization & Deserialization**
- **Database Latency**
- **Disk I/O**
- **Garbage Collection**
- **Lock Contention**
- **External API Latency**
