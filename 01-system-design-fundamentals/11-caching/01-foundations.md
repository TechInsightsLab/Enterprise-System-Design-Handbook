# Caching

> **Part I – System Design Foundations**

---

# Caching

| Attribute | Value |
|-----------|--------|
| **Version** | 2.0 |
| **Part** | Part I – System Design Foundations |
| **Chapter** | 11 |
| **Reading Time** | ~350–450 Minutes |
| **Difficulty** | Advanced |
| **Last Updated** | July 2026 |

---

> [!NOTE]
> **Caching is the practice of temporarily storing frequently accessed data in faster storage to reduce latency, improve throughput, decrease infrastructure costs, and reduce load on slower backend systems.**

Almost every large-scale system uses caching.

Google.

Amazon.

Netflix.

Uber.

Facebook.

LinkedIn.

Banking systems.

Cloud platforms.

Without caching,

many modern internet services simply could not scale economically.

---

# Table of Contents

## Part 1 – Foundations

- Learning Objectives
- Why Caching Matters
- Business Story
- Cache Fundamentals
- Why Systems Become Slow
- Cache Terminology
- Cache Architecture
- Cache Hierarchy
- Cache Lifecycle

## Part 2 – Cache Types

- CPU Cache
- OS Page Cache
- Browser Cache
- CDN Cache
- Reverse Proxy Cache
- Application Cache
- Distributed Cache
- Database Cache
- Query Cache
- Object Cache
- Session Cache

## Part 3 – Cache Patterns

- Cache Aside (Lazy Loading)
- Read Through
- Write Through
- Write Behind (Write Back)
- Refresh Ahead
- Cache Warming
- Cache Prefetching

## Part 4 – Cache Management

- TTL
- Expiration
- Invalidation
- Eviction
- LRU
- LFU
- FIFO
- Random Replacement

## Part 5 – Distributed Caching

- Redis
- Memcached
- Hazelcast
- Apache Ignite
- Redis Cluster
- Redis Sentinel
- Multi-Level Cache
- Near Cache
- Multi-Region Cache

## Part 6 – Cache Problems

- Cache Stampede
- Cache Avalanche
- Cache Penetration
- Hot Keys
- Cold Cache
- Split Brain
- Cache Consistency

## Part 7 – Enterprise Architecture

- Microservice Caching
- CDN
- Event-Driven Cache Invalidation
- Kafka + CDC
- Cloud Cache Services

## Part 8 – Production

- Monitoring
- Metrics
- Cost Optimization
- Security
- Production Incidents
- Architecture Review Checklist
- ADR

## Part 9 – Interview Preparation

- Beginner Questions
- Senior Architect Questions
- Principal Architect Questions

## Part 10 – Summary

- Cheat Sheet
- Golden Principles
- Chapter Summary

---

# Learning Objectives

After completing this chapter, you should be able to:

- Explain why caching is critical for scalable systems.
- Choose appropriate caching strategies.
- Design distributed caching architectures.
- Understand Redis internals.
- Prevent common cache failures.
- Design cache consistency strategies.
- Build cost-efficient caching layers.
- Answer enterprise architecture interview questions.

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

---

# Previous Chapters

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

---

# Next Chapters

- Event-Driven Architecture
- Messaging
- API Design
- Security
- Observability

---

# Opening Business Story

Imagine building an online shopping platform.

Initially,

only:

```
100 Users
```

Everything works perfectly.

Every request directly queries:

```
Database
```

---

After one year:

```
10 Million Users
```

Every request now performs:

```text
User Request

↓

Application

↓

Database
```

The database becomes overloaded.

---

Users complain:

- Pages load slowly.
- Checkout takes too long.
- Search is delayed.
- Recommendations fail.

The application code hasn't changed much.

The business simply became successful.

---

# The Performance Bottleneck

Assume:

Database response time:

```
20 ms
```

Each page requires:

```
15 Database Queries
```

Total:

```
300 ms
```

Now add:

- Network latency
- Authentication
- Business logic

Response time exceeds:

```
1 Second
```

Users begin abandoning the website.

---

# Business Reality

Studies consistently show that:

Small increases in page load time often reduce:

- Conversion rate
- Customer satisfaction
- Revenue

Performance directly affects business outcomes.

---

# The Solution

Instead of asking the database repeatedly,

store frequently accessed data closer to the application.

```text
Application

↓

Cache

↓

Database
```

Now:

Most requests never reach the database.

---

# Example

Product:

```
iPhone 18
```

Viewed:

```
100,000 Times Today
```

Did the product change:

```
100,000 Times?
```

No.

So why execute:

```
100,000

Database Queries?
```

Store it in cache.

---

# Definition

Caching is:

> **The temporary storage of frequently accessed or expensive-to-compute data in a faster storage layer so future requests can be served with significantly lower latency.**

Notice:

Temporary.

Not permanent.

---

# Why Caching Matters

Caching improves:

- Response Time
- Throughput
- Scalability
- Availability
- Cost Efficiency
- User Experience

Few architectural techniques provide such broad benefits.

---

# Real Example

Without cache:

```text
Request

↓

Database

↓

20 ms
```

With cache:

```text
Request

↓

Memory

↓

0.2 ms
```

Around:

```
100×

Faster
```

Exact improvements vary by workload and infrastructure, but in-memory access is typically orders of magnitude faster than disk-backed database queries.

---

# Everyday Analogy

Think of your office desk.

Frequently used items:

- Laptop
- Notebook
- Pen

are kept:

On the desk.

Rarely used documents remain:

In a cabinet.

The desk acts as:

```
Cache
```

The cabinet acts as:

```
Database
```

You optimize access time.

---

# Memory Hierarchy Analogy

Computers themselves use multiple caches.

```text
CPU Registers

↓

L1 Cache

↓

L2 Cache

↓

L3 Cache

↓

RAM

↓

SSD

↓

Disk
```

Closer storage:

Smaller.

Faster.

More expensive.

Farther storage:

Larger.

Slower.

Cheaper.

Enterprise caching follows the same principle.

---

# Why Systems Become Slow

Applications spend time waiting for:

- Database
- Network
- File System
- Third-party APIs
- Authentication
- Disk I/O

Most delays come from waiting,

not computing.

Caching removes much of that waiting.

---

# Cache Terminology

| Term | Meaning |
|------|----------|
| Cache | Temporary fast storage |
| Cache Hit | Requested data found in cache |
| Cache Miss | Data absent from cache |
| Cache Fill | Loading data into cache |
| Cache Eviction | Removing cached data |
| TTL | Time-To-Live |
| Cache Warm | Cache already populated |
| Cold Cache | Empty cache |

---

# Cache Hit

Best case.

```text
Request

↓

Cache

↓

Return Data
```

Database never accessed.

---

# Cache Miss

Data unavailable.

```text
Request

↓

Cache

↓

Database

↓

Cache Updated

↓

Response
```

Future requests become faster.

---

# Hit Ratio

One of the most important cache metrics.

Formula:

```
Cache Hits

/

Total Requests
```

Example:

```
900 Hits

100 Misses
```

Hit Ratio:

```
90%
```

Higher hit ratios generally reduce backend load, although the ideal target depends on the workload.

---

# Cache Architecture

Simple architecture:

```text
Client

↓

Application

↓

Cache

↓

Database
```

Cache acts as a protective layer.

---

# Benefits

Database receives:

Only cache misses.

Instead of:

Every request.

This dramatically reduces:

- CPU
- Memory
- Disk I/O
- Network

---

# Cache Lifecycle

Every cached object follows a lifecycle.

```text
Create

↓

Store

↓

Read

↓

Expire

↓

Evict

↓

Reload
```

Architects design each stage carefully.

---

# Where Can Caches Exist?

Caching occurs at many layers.

```text
CPU

↓

Operating System

↓

Browser

↓

CDN

↓

Load Balancer

↓

Application

↓

Distributed Cache

↓

Database
```

Enterprise systems typically use multiple caches simultaneously.

---

# What Should Be Cached?

Ideal candidates:

- Product Catalog
- User Profiles
- Configuration
- Exchange Rates
- Country Lists
- Product Images
- Search Results
- Frequently Read Data

---

# What Should Not Be Cached?

Examples:

- One-time passwords (OTPs)
- Rapidly changing balances (unless carefully designed)
- Real-time stock prices (unless stale data is acceptable)
- Highly sensitive secrets
- Frequently updated transactional data without a consistency strategy

Always evaluate:

Business correctness first.

---

# Characteristics of Good Cache Data

Good candidates usually have:

- High read frequency
- Low update frequency
- Expensive computation
- Expensive database queries
- Large fan-out
- Reusable across requests

---

# Cache as a Performance Multiplier

Consider:

Database capacity:

```
10,000 Queries/sec
```

Cache hit ratio:

```
95%
```

Application receives:

```
200,000 Requests/sec
```

Database only serves:

```
10,000 Misses/sec
```

Caching effectively multiplies backend capacity.

---

# Common Misconceptions

### Myth

Cache is only for performance.

**Reality**

Caching also improves:

- Scalability
- Availability
- Cost optimization

---

### Myth

Everything should be cached.

**Reality**

Caching introduces:

- Consistency challenges
- Memory costs
- Operational complexity

Only cache when it provides measurable value.

---

### Myth

Cache always returns correct data.

**Reality**

Depending on strategy,

cached data may be stale.

Architects must decide whether that is acceptable.

---

# Architect's Perspective

Junior engineers often view caching as:

> "A way to make applications faster."

Experienced architects view caching as:

> **A strategic architectural layer that protects expensive resources while balancing performance, consistency, operational complexity, and cost.**

Every cache introduces trade-offs.

The key architectural questions become:

- What should be cached?
- Where should it be cached?
- How long should it remain cached?
- Who invalidates it?
- What happens when the cache fails?

Answering these correctly distinguishes a production-ready architecture from one that performs well only in development.

---

**End of Part 1**

The next part will cover:

- **CPU Cache**
- **Operating System Page Cache**
- **Browser Cache**
- **HTTP Cache**
- **Reverse Proxy Cache**
- **CDN**
- **Application Cache**
- **Database Cache**
- **Distributed Cache**
- **Cache Hierarchy**
