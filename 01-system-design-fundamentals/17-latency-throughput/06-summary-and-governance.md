
# 48. Performance Budgets

> **A Performance Budget is a predefined limit on performance-related metrics that a system must satisfy throughout its lifecycle.**

Just as every project has:

- Budget
- Timeline
- Resources

A software system should also have.

A performance budget.

Without one.

Performance gradually degrades.

As new features are added.

---

# Why Performance Budgets Matter

Imagine.

A frontend application.

Initially loads in.

```
1.2 Seconds
```

Every release adds.

- More JavaScript
- More CSS
- Larger images
- More API calls

Six months later.

```
6.5 Seconds
```

Nobody intentionally made it slow.

Performance degraded.

Gradually.

A performance budget prevents this.

---

# Examples

Typical budgets include.

### API Latency

```text
P95

<200 ms
```

---

### Page Load

```text
First Contentful Paint

<1.5 Seconds
```

---

### Throughput

```text
Support

20,000 RPS
```

---

### Error Rate

```text
<0.1%
```

---

### CPU

```text
<70%

At Peak Load
```

---

### Memory

```text
<75%

Heap Usage
```

---

# Enterprise Example

Online Banking.

Performance Budget.

```text
Login

<300 ms

----------------------

Balance Inquiry

<150 ms

----------------------

Fund Transfer

<500 ms

----------------------

P99

<800 ms
```

Engineering teams.

Must satisfy.

These limits.

Before release.

---

# Budget Categories

Performance budgets can exist at different layers.

```text
Client

↓

API

↓

Application

↓

Database

↓

Network

↓

Infrastructure
```

Every layer.

Should have.

Defined targets.

---

# Monitoring Performance Budgets

Performance budgets.

Should not exist.

Only in documentation.

They should be continuously monitored.

Example.

```text
Latency

↓

Dashboard

↓

Alert

↓

Investigation
```

Budgets become.

Operational objectives.

---

# Benefits

Performance budgets help.

- Prevent gradual degradation
- Maintain SLAs
- Guide architecture decisions
- Support capacity planning
- Improve customer experience

---

# Architect's Perspective

Performance should be treated.

Like a feature.

Every release.

Must remain.

Within acceptable limits.

---

# 49. Performance Anti-Patterns

Many enterprise systems.

Become slow.

Not because hardware is insufficient.

But because.

Architectural mistakes.

Accumulate over time.

---

# Anti-Pattern 1

## Chatty Communication

Instead of.

One request.

Applications make.

Hundreds.

Of small requests.

---

# Example

```text
Client

↓

API 1

↓

API 2

↓

API 3

↓

API 4

↓

API 5
```

Each network call.

Adds latency.

---

# Better

```text
Client

↓

Single Aggregated API
```

Fewer round trips.

Lower latency.

---

# Anti-Pattern 2

## N+1 Queries

Classic database problem.

---

Bad.

```text
Orders

↓

Query Customer

↓

Query Product

↓

Query Inventory

(Repeated)
```

Hundreds.

Of database calls.

---

Better.

```text
JOIN

↓

Single Query
```

Or batch loading.

---

# Anti-Pattern 3

## Synchronous Everything

Every operation.

Blocks.

The user.

---

Example.

```text
Checkout

↓

Email

↓

SMS

↓

Analytics

↓

Payment

↓

Response
```

Customer waits.

For everything.

---

Better.

```text
Checkout

↓

Payment

↓

Response

↓

Background Queue

↓

Email

↓

Analytics
```

---

# Anti-Pattern 4

## Database as the Bottleneck

Every request.

Reads.

Writes.

Database.

Even.

Frequently requested data.

---

Better.

Introduce.

Caching.

Read replicas.

Materialized views.

Where appropriate.

---

# Anti-Pattern 5

## Large Payloads

Returning.

Entire objects.

When only.

Three fields.

Are needed.

---

Example.

```text
Customer

↓

500 Fields
```

Client needs.

Only.

```text
Name

Status

ID
```

Transfer only required data.

---

# Anti-Pattern 6

## Overfetching

REST.

Sometimes returns.

More data.

Than required.

---

Example.

```text
GET /customers
```

Returns.

Complete customer profile.

When UI needs.

Only.

Customer name.

---

Solutions.

- GraphQL
- Projection APIs
- DTOs
- Selective fields

---

# Anti-Pattern 7

## Underfetching

Opposite problem.

UI calls.

Many APIs.

To build one screen.

Example.

```text
Customer

↓

Orders

↓

Payments

↓

Address

↓

Notifications
```

Too many round trips.

---

# Anti-Pattern 8

## Large Database Transactions

Huge transactions.

Hold locks longer.

Increase latency.

Reduce throughput.

Prefer.

Smaller.

Focused transactions.

---

# Anti-Pattern 9

## Blocking I/O Everywhere

Threads wait.

For.

Network.

Disk.

Database.

Resources remain idle.

Modern architectures.

Often use asynchronous I/O.

Where appropriate.

---

# Anti-Pattern 10

## Ignoring Tail Latency

Average.

Looks excellent.

Customers still complain.

Always monitor.

P95.

P99.

---

# Anti-Pattern 11

## Scaling Without Identifying Bottlenecks

Adding.

More application servers.

When.

Database.

Is saturated.

Provides little benefit.

---

# Anti-Pattern 12

## Premature Optimization

Optimizing.

Code paths.

Never executed.

Measure first.

Optimize second.

---

# Architect's Perspective

Performance optimization.

Should always begin.

With.

Measurement.

Not assumptions.

---

# 50. Enterprise Case Study

## Global E-Commerce Platform

Architecture.

```text
Users

↓

CDN

↓

Load Balancer

↓

API Gateway

↓

Order Service

↓

Inventory

↓

Payment

↓

Kafka

↓

Database
```

---

# Initial Problems

During.

Annual sale.

Traffic increased.

10x.

Observed.

- P99 = 7 Seconds
- Database CPU = 98%
- Queue backlog
- Checkout failures

Revenue declined.

Despite sufficient hardware.

---

# Investigation

Observability revealed.

Major bottlenecks.

- N+1 queries
- No caching
- Large payloads
- Blocking payment calls
- Small database connection pool

---

# Improvements

Architects implemented.

- Redis caching
- Connection pooling
- Batch loading
- Async notifications
- Read replicas
- Autoscaling
- gRPC internally
- HTTP/2
- CDN optimization

---

# Results

After optimization.

```text
P95

1.8 Seconds

↓

180 ms

------------------------

P99

7 Seconds

↓

450 ms

------------------------

Throughput

15K TPS

↓

75K TPS

------------------------

Checkout Errors

↓

90% Reduction
```

Revenue increased.

Without rewriting.

The entire platform.

---

# Lessons

Performance improvements.

Often come.

From.

Architectural changes.

Rather than.

Faster CPUs.

---

# 51. Architecture Review Checklist

Before production.

Architects should verify.

---

## Latency

✓ Cache strategy defined.

✓ Database indexes reviewed.

✓ Connection pooling configured.

✓ Payload size optimized.

✓ Compression evaluated.

✓ Read replicas considered.

✓ Async processing identified.

✓ External API timeouts configured.

---

## Throughput

✓ Horizontal scaling supported.

✓ Load balancing configured.

✓ Partitioning strategy reviewed.

✓ Queue capacity planned.

✓ Autoscaling configured.

✓ Backpressure implemented.

✓ Flow control validated.

---

## Measurement

✓ P95 monitored.

✓ P99 monitored.

✓ Tail latency tracked.

✓ Throughput dashboards available.

✓ Queue metrics monitored.

✓ CPU measured.

✓ Memory measured.

✓ GC monitored.

---

## Capacity

✓ Peak traffic estimated.

✓ Growth projected.

✓ Safety margin included.

✓ Load tests executed.

✓ Stress tests executed.

✓ Soak tests completed.

---

## Operations

✓ Performance budgets defined.

✓ Alerts configured.

✓ Runbooks available.

✓ Rollback strategy verified.

---

# 52. Interview Questions

## Beginner

- What is latency?
- What is throughput?
- Difference between latency and response time?
- Difference between bandwidth and throughput?
- Why is caching useful?

---

## Intermediate

- Explain connection pooling.
- Explain batching.
- Explain pagination.
- Explain asynchronous processing.
- Explain HTTP/2 improvements.
- Explain gRPC advantages.

---

## Senior Engineer

- Reduce API latency.
- Increase database throughput.
- Optimize a payment service.
- Improve P99 latency.
- Design low-latency microservices.

---

## Solution Architect

- Design a high-throughput payment gateway.
- Design an API serving 1 million RPS.
- Build global low-latency architecture.
- Optimize checkout performance.
- Explain CDN strategy.

---

## Principal Architect

- Define enterprise performance budgets.
- Build organization-wide performance strategy.
- Optimize global multi-region architecture.
- Create performance governance model.
- Design for predictable latency at massive scale.

---

# One-Page Revision Cheat Sheet

## Latency

- Network
- DNS
- TLS
- Serialization
- Database
- Disk
- GC
- Locks
- External APIs

---

## Latency Optimization

- Cache
- Pooling
- Compression
- Batching
- Pagination
- Async
- Read Replicas
- CDN
- Edge
- HTTP/2
- HTTP/3
- gRPC

---

## Throughput Optimization

- Horizontal Scaling
- Load Balancer
- Partitioning
- Sharding
- Worker Pools
- Parallelism
- Queues
- Autoscaling
- Backpressure
- Flow Control

---

## Measurement

- Average
- Median
- P50
- P95
- P99
- Tail Latency
- Little's Law
- Capacity Planning

---

## Testing

- Benchmark
- Load
- Stress
- Spike
- Soak

---

## Anti-Patterns

- Chatty APIs
- N+1 Queries
- Blocking I/O
- Large Payloads
- No Cache
- Premature Optimization
- Scaling Wrong Component
- Ignoring Tail Latency

---

# Architect's Golden Rules

1. Optimize the biggest bottleneck first.
2. Measure before optimizing.
3. Monitor P95 and P99, not just averages.
4. Reduce network round trips.
5. Cache expensive operations.
6. Keep payloads small.
7. Use asynchronous processing where appropriate.
8. Design for horizontal scalability.
9. Protect systems with backpressure.
10. Define and enforce performance budgets.

---

# Chapter Summary

Latency and Throughput are two of the most critical quality attributes in system design.

Although related, they answer different questions:

- **Latency** measures how long a single request takes to complete.
- **Throughput** measures how much work the system can complete over time.

Designing high-performance systems requires understanding every contributor to latency—from DNS resolution and network hops to database access, serialization, garbage collection, and external dependencies.

Reducing latency is achieved through techniques such as caching, connection pooling, efficient protocols, asynchronous processing, CDNs, and edge computing.

Increasing throughput requires architectural strategies like horizontal scaling, load balancing, partitioning, sharding, worker pools, queue-based architectures, autoscaling, and effective backpressure.

Equally important is **measuring performance correctly**. Architects should focus on percentile metrics (P95, P99), tail latency, and realistic load testing rather than relying solely on average response times.

Ultimately, performance is not about achieving the fastest benchmark in a laboratory. It is about delivering **predictable, consistent, and scalable performance under real-world production workloads**, even during peak demand.

---

# Connection to Previous Chapters

| Chapter | Primary Question |
|---------|------------------|
| Performance | How can the system execute efficiently? |
| Caching | How can repeated work be avoided? |
| Messaging | How can workloads be decoupled? |
| API Design | How can communication be efficient? |
| Observability | How do we measure performance? |
| Maintainability | How do we sustain performance improvements? |
| **Latency & Throughput** | **How do we deliver fast responses while processing massive workloads predictably?** |

Latency and Throughput unify many previous architectural concepts into a practical framework for designing systems that remain responsive and scalable as demand grows.

---

> **Chapter 17 – Latency & Throughput Complete**

