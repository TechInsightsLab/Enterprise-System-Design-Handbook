
# 13. Performance Mechanisms

> **Architecture decisions define *what* should be optimized. Performance mechanisms define *how* those optimizations are implemented using specific technologies and platforms.**

Multiple organizations may solve the same performance problem using different technology stacks.

For example:

| Performance Problem | Possible Technologies |
|---------------------|-----------------------|
| Distributed Cache | Redis, Memcached, Hazelcast |
| Connection Pooling | HikariCP, Apache DBCP |
| Profiling | JProfiler, VisualVM, Async Profiler |
| Load Testing | JMeter, Gatling, k6 |
| Distributed Tracing | OpenTelemetry, Jaeger, Zipkin |
| CDN | CloudFront, Azure CDN, Cloud CDN |

Technology changes frequently.

Performance engineering principles remain stable.

---

# Performance Optimization Flow

```text
Business Requirement

↓

Performance Objective

↓

Identify Bottleneck

↓

Architecture Decision

↓

Implementation Mechanism

↓

Measurement

↓

Continuous Optimization
```

Performance tuning without measurements frequently creates unnecessary complexity.

---

# 13.1 JVM Performance

Java powers many enterprise systems.

Architects therefore need a strong understanding of JVM behavior.

---

# JVM Execution Model

```text
Application

↓

JVM

↓

Heap

Stack

↓

Garbage Collector

↓

Operating System

↓

Hardware
```

Every request eventually consumes JVM resources.

---

# Heap Management

The JVM Heap stores application objects.

```text
Heap

↓

Young Generation

↓

Old Generation
```

---

## Small Heap

Advantages

- Lower memory usage

Disadvantages

- Frequent GC

---

## Large Heap

Advantages

- Fewer collections

Disadvantages

- Longer GC pauses

Architects balance heap size against latency requirements.

---

# Garbage Collection

Garbage Collection automatically reclaims unused memory.

However,

GC pauses contribute directly to application latency.

---

## Common Collectors

| Collector | Best Use Case |
|------------|---------------|
| G1 GC | General enterprise applications |
| ZGC | Ultra-low latency |
| Shenandoah | Large heaps |
| Serial GC | Small applications |

---

# Choosing a Collector

Example:

Trading Platform

↓

Ultra-low latency

↓

ZGC

---

Batch Processing

↓

Maximum throughput

↓

G1 GC

---

Selection depends on business priorities.

---

# Virtual Threads (Java 21)

Virtual Threads significantly increase concurrency.

Traditional model:

```text
Request

↓

Platform Thread
```

Virtual Thread model:

```text
Request

↓

Virtual Thread

↓

Carrier Thread
```

---

## Benefits

- Massive concurrency
- Lower thread overhead
- Simpler asynchronous code
- Better IO scalability

---

## Suitable Workloads

- REST APIs
- Database applications
- Microservices
- Network servers

---

## Less Suitable

Pure CPU-intensive workloads benefit less because CPU cores remain the limiting factor.

---

# Escape Analysis

The JVM may eliminate unnecessary object allocations.

Benefits include:

- Reduced allocations
- Less GC pressure
- Higher throughput

Developers receive these optimizations automatically when code is written appropriately.

---

# JIT Compilation

Frequently executed methods become native machine code.

```text
Java Bytecode

↓

JIT Compiler

↓

Machine Code
```

Benefits:

- Faster execution
- Better optimization
- Adaptive compilation

---

# 13.2 Spring Boot Performance

Spring Boot provides numerous mechanisms for improving performance.

---

# Connection Pooling

Default recommendation:

HikariCP

Benefits:

- Low latency
- Efficient connection reuse
- Excellent production performance

---

# Spring Cache

Supports:

- Redis
- Caffeine
- Hazelcast

Example:

```java
@Cacheable
```

Frequently accessed results avoid repeated computation.

---

# Asynchronous Processing

```java
@Async
```

Long-running work executes independently.

---

# Scheduling

```java
@Scheduled
```

Background processing reduces user-facing latency.

---

# HTTP Compression

Spring Boot supports:

```
gzip

brotli
```

Reducing payload size.

---

# 13.3 Redis Performance

Redis is among the highest-impact performance technologies.

---

# Typical Uses

- API cache
- Session store
- Leaderboards
- Rate limiting
- Distributed locking

---

# Performance Advantages

Typical latency:

```
Sub-millisecond
```

Compared to databases:

```
Several milliseconds

to

Hundreds of milliseconds
```

---

# Redis Data Structures

| Structure | Typical Use |
|-----------|-------------|
| String | Cache |
| Hash | User Profiles |
| List | Queues |
| Set | Unique Values |
| Sorted Set | Rankings |
| Stream | Event Processing |

---

# Common Optimizations

- Appropriate TTL
- Key naming conventions
- Memory monitoring
- Pipelining
- Connection pooling

---

# 13.4 Kafka Performance

Kafka handles extremely high throughput.

Performance depends on proper configuration.

---

# Producer Optimization

Important parameters:

- Batch Size
- Compression
- Linger Time
- Acknowledgement Mode

Example:

```
Batch

↓

Compression

↓

Broker
```

Fewer network requests improve throughput.

---

# Consumer Optimization

Examples:

- Consumer Groups
- Parallel Consumers
- Larger Fetch Size
- Appropriate Poll Interval

---

# Partitioning

More partitions increase parallelism.

However,

Too many partitions increase operational complexity.

Architects balance throughput with maintainability.

---

# 13.5 Database Performance

Most enterprise applications spend significant time waiting for databases.

Database optimization often produces the highest return.

---

# Index Optimization

Without index:

```
Table Scan

↓

Millions of Rows
```

With index:

```
Index Lookup

↓

Target Rows
```

---

# Query Optimization

Avoid:

- SELECT *
- Cartesian joins
- Unnecessary sorting
- Repeated subqueries

---

# Connection Pool

Applications should reuse connections.

Benefits:

- Lower latency
- Reduced database overhead

---

# Read Replicas

Heavy read workloads benefit from replication.

```text
Application

↓

Primary

↓

Replica

↓

Replica
```

---

# Materialized Views

Expensive reports become precomputed.

Response times improve significantly.

---

# 13.6 Kubernetes Performance

Kubernetes introduces flexibility.

Proper configuration prevents resource waste.

---

# Resource Requests

Specify minimum resources.

Example:

```yaml
requests:
  cpu: "500m"
```

---

# Resource Limits

Prevent noisy neighbors.

```yaml
limits:
  cpu: "2"
```

---

# Horizontal Pod Autoscaler

```text
CPU

70%

↓

Scale Pods
```

---

# Vertical Pod Autoscaler

Adjusts container resources.

Useful for workloads with changing resource requirements.

---

# Node Affinity

Place workloads near:

- Storage
- GPUs
- Specialized hardware

Reducing latency.

---

# 13.7 AWS Performance Services

| Requirement | AWS Service |
|-------------|-------------|
| CDN | CloudFront |
| Cache | ElastiCache |
| Database | Aurora |
| Monitoring | CloudWatch |
| Tracing | X-Ray |
| Load Testing | Distributed Load Testing |
| Compute | EC2 / ECS / EKS / Lambda |

---

# Typical AWS Architecture

```text
CloudFront

↓

ALB

↓

EKS

↓

Redis

↓

Aurora
```

---

# 13.8 Azure Performance Services

| Requirement | Azure Service |
|-------------|---------------|
| CDN | Azure CDN |
| Cache | Azure Cache for Redis |
| Database | Azure SQL |
| Monitoring | Azure Monitor |
| Kubernetes | AKS |

---

# 13.9 Google Cloud Performance Services

| Requirement | GCP Service |
|-------------|-------------|
| CDN | Cloud CDN |
| Cache | Memorystore |
| Database | Cloud SQL |
| Monitoring | Cloud Monitoring |
| Kubernetes | GKE |

---

# 13.10 Profiling

Profiling reveals where applications actually spend time.

Without profiling,

optimization becomes guesswork.

---

# Types of Profilers

| Tool | Typical Use |
|------|-------------|
| VisualVM | JVM Monitoring |
| JProfiler | Enterprise Profiling |
| Async Profiler | Low-overhead CPU Profiling |
| Java Flight Recorder | Production Analysis |

---

# Common Metrics

Profile:

- CPU
- Memory
- Allocations
- Lock contention
- Thread states
- GC pauses

---

# 13.11 Benchmarking

Benchmarks provide repeatable performance measurements.

---

# Microbenchmark

Measure:

```
Single Method
```

Useful for:

- Algorithms
- Collections
- Serialization

Java:

JMH

---

# System Benchmark

Measure:

Entire application.

Examples:

- Login
- Checkout
- Search

---

# 13.12 Load Testing

Measure expected production traffic.

Common tools:

- Apache JMeter
- Gatling
- k6

---

# Stress Testing

Increase traffic until failure.

Purpose:

Discover limits.

---

# Spike Testing

Sudden traffic increases.

Example:

```
2K RPS

↓

50K RPS

↓

2K RPS
```

Measure recovery.

---

# Soak Testing

Long-duration testing.

Example:

```
48 Hours

Continuous Load
```

Detect:

- Memory leaks
- Connection leaks
- Resource exhaustion

---

# 13.13 Observability

Performance requires visibility.

Monitor:

- Response time
- P95
- P99
- Throughput
- CPU
- Memory
- Database latency
- Queue depth
- Cache hit ratio

---

# Business Metrics

Also monitor:

- Checkout completion
- Search latency
- Revenue/hour
- Active users
- Orders/minute

Performance should always connect to business outcomes.

---

# Performance Mechanism Selection Matrix

| Problem | Recommended Mechanism |
|----------|-----------------------|
| Slow Reads | Redis |
| Slow SQL | Index + Query Optimization |
| High Connection Cost | HikariCP |
| Long-running Tasks | @Async + Queue |
| JVM Pauses | ZGC / Shenandoah |
| High Concurrency | Virtual Threads |
| Global Latency | CDN |
| CPU Bottleneck | Better Algorithm |
| High Throughput Messaging | Kafka |
| API Profiling | Java Flight Recorder |

---

# Mechanism Comparison

| Mechanism | Primary Benefit | Common Trade-off |
|-----------|-----------------|------------------|
| Redis | Low Latency | Cache Consistency |
| HikariCP | Faster DB Access | Pool Tuning |
| Kafka | High Throughput | Operational Complexity |
| Virtual Threads | Massive Concurrency | Requires Modern JVM |
| CDN | Lower Global Latency | Cache Invalidation |
| JFR | Production Profiling | Analysis Effort |
| Kubernetes HPA | Automatic Scaling | Scaling Delay |

---

# Architect's Perspective

Performance improvements should follow a disciplined order of optimization.

A typical sequence is:

1. Measure the problem using profiling and monitoring.
2. Optimize algorithms and eliminate unnecessary work.
3. Improve database queries and indexing.
4. Introduce caching where appropriate.
5. Reduce network overhead through batching or compression.
6. Execute independent tasks in parallel or asynchronously.
7. Tune JVM and runtime settings.
8. Scale infrastructure only after software inefficiencies have been addressed.

This order minimizes cost while maximizing business value.

Adding hardware to compensate for inefficient software is rarely the most economical long-term solution.

---

**End of Part 5**

The next section will cover:

- **Performance Trade-offs**
- **Performance Measurements**
- **Benchmarking Methodology**
- **Capacity Planning**
- **SLO, SLA, and SLI**
- **Performance Budgets in Depth**
- **Operational Performance Considerations**
- **Cost Optimization**
- **Resource Utilization Analysis**
- **Performance Engineering Lifecycle**
