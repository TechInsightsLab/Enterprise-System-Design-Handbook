
# 80. Monitoring Concurrent Systems

> **A concurrent system that cannot be observed cannot be reliably operated.**

Concurrency bugs often appear only under:

- High load
- Production traffic
- Rare timing conditions
- Hardware differences

Unlike syntax errors,

they rarely fail consistently.

Observability becomes essential.

---

# What Should Be Monitored?

Concurrency affects every layer.

```text
Business

↓

Application

↓

JVM

↓

Operating System

↓

Infrastructure
```

Each layer provides valuable signals.

---

# Business Metrics

Business failures usually indicate concurrency problems before technical metrics do.

Monitor:

- Duplicate Orders
- Duplicate Payments
- Inventory Oversell
- Failed Transactions
- Retry Count
- Timeout Count
- Compensation Count
- Order Completion Rate

---

# Application Metrics

Important metrics:

- Active Threads
- Waiting Threads
- Blocked Threads
- Thread Pool Utilization
- Queue Length
- Task Execution Time
- Lock Wait Time
- Request Latency

---

# JVM Metrics

Critical JVM concurrency metrics include:

| Metric | Why It Matters |
|----------|----------------|
| Live Threads | Detect thread leaks |
| Peak Threads | Capacity planning |
| Blocked Threads | Lock contention |
| Deadlocked Threads | System health |
| GC Pause Time | Affects thread responsiveness |
| Heap Usage | Memory pressure |

---

# Infrastructure Metrics

Monitor:

- CPU Utilization
- Context Switches
- Load Average
- Memory Usage
- Disk I/O
- Network Latency
- Container CPU Limits

---

# Dashboard Example

```text
Requests/sec

18,200

-------------------

Active Threads

184

-------------------

Blocked Threads

2

-------------------

Queue Size

14

-------------------

Average Latency

42 ms

-------------------

CPU

68%

-------------------

Context Switches

11K/sec
```

---

# Thread Pool Dashboard

Monitor:

```text
Pool Size

200

↓

Active

180

↓

Idle

20

↓

Queue

35

↓

Rejected

0
```

Thread pool saturation often appears before user-visible failures.

---

# Queue Monitoring

Producer-Consumer systems require queue monitoring.

Examples:

- Kafka Consumer Lag
- RabbitMQ Queue Depth
- SQS Approximate Messages Visible

Growing queues may indicate:

- Slow consumers
- Resource starvation
- Downstream failures

---

# Lock Metrics

Useful metrics include:

- Lock Wait Time
- Lock Acquisition Rate
- Lock Contention
- Deadlock Count

Excessive lock contention often signals scalability bottlenecks.

---

# 81. Thread Dumps

One of the most powerful debugging tools for Java concurrency.

A thread dump captures:

The state of every thread at a specific moment.

---

# Thread States

Common thread states:

| State | Meaning |
|---------|----------|
| NEW | Created but not started |
| RUNNABLE | Ready or running |
| BLOCKED | Waiting for monitor lock |
| WAITING | Waiting indefinitely |
| TIMED_WAITING | Waiting with timeout |
| TERMINATED | Finished execution |

---

# Example

```text
Thread-45

BLOCKED

Waiting to lock

OrderService
```

Immediately identifies contention.

---

# Deadlock Detection

JVM can detect:

```text
Found One Java-Level Deadlock
```

Thread dump identifies:

- Threads involved
- Locks owned
- Locks requested

---

# Useful Commands

Linux:

```bash
jstack <PID>
```

Modern JVM:

```bash
jcmd <PID> Thread.print
```

These tools are invaluable during production incidents.

---

# Thread Dump Analysis

Questions to ask:

- Are many threads BLOCKED?
- Is one lock causing contention?
- Are threads waiting on database calls?
- Are thread pools exhausted?

---

# 82. Java Flight Recorder (JFR)

Java Flight Recorder (JFR) is a low-overhead profiling and diagnostics tool built into the JVM.

It records:

- Thread activity
- Lock contention
- CPU usage
- Allocation rates
- Garbage collection
- Method execution

with minimal production overhead.

---

# Why JFR?

Traditional profilers may slow applications significantly.

JFR is designed for:

Production environments.

---

# Captured Events

Examples:

- Thread Sleep
- Lock Wait
- Method Profiling
- Object Allocation
- File I/O
- Socket I/O
- Garbage Collection

---

# Typical Workflow

```text
Application

↓

JFR Recording

↓

Recording File

↓

Analysis

↓

Performance Improvements
```

---

# Benefits

- Low overhead
- Production safe
- Excellent concurrency diagnostics
- Integrated with Java Mission Control (JMC)

---

# 83. Production Incident

## Thread Pool Exhaustion

E-commerce platform.

Traffic spike.

Application:

```
200 Thread Pool
```

Payment API became slow.

Every request waited:

```
5 Seconds
```

---

# Result

```text
200 Threads

↓

Waiting

↓

Queue

↓

Growing

↓

Timeouts
```

New requests could not execute.

---

# Root Cause

Blocking HTTP calls inside request threads.

---

# Solution

Implemented:

- Async processing
- Circuit Breaker
- Timeouts
- Bulkhead isolation
- Larger queue with monitoring

---

# Lesson

Thread pools protect resources,

but blocked threads still consume capacity.

---

# Production Incident

## Deadlock During Flash Sale

Inventory:

```
10 Items
```

Thousands of concurrent purchases.

Two services acquired locks in different orders.

---

# Symptoms

- Requests never completed
- CPU remained low
- Active threads remained constant
- Customer complaints increased

---

# Root Cause

Inconsistent lock ordering.

---

# Solution

Standardized lock acquisition order.

Added lock timeout metrics.

---

# Production Incident

## Kafka Consumer Lag

Consumers:

```
20
```

Partitions:

```
20
```

Database became slow.

Consumers processed:

```
10 Messages/sec
```

Producers generated:

```
500 Messages/sec
```

---

# Result

Consumer lag increased rapidly.

---

# Solution

- Optimized database queries
- Increased partition count
- Tuned consumer processing
- Added backpressure alerts

---

# Production Incident

## Virtual Thread Misuse

Team migrated everything to:

```
Virtual Threads
```

Application performed:

Large CPU-intensive image processing.

Performance decreased.

---

# Root Cause

Virtual Threads improve concurrency for blocking I/O,

not CPU-bound computation.

---

# Solution

Moved image processing to:

ForkJoinPool.

Used Virtual Threads for REST requests only.

---

# Lessons Learned

| Incident | Improvement |
|-----------|-------------|
| Thread Pool Exhaustion | Async + Timeouts |
| Deadlock | Lock Ordering |
| Kafka Lag | Scale Consumers + Optimize DB |
| Virtual Thread Misuse | Use Appropriate Execution Model |

---

# 84. Common Anti-patterns

> **Most concurrency failures are caused by architectural anti-patterns rather than language limitations.**

---

# Anti-pattern 1

## Shared Mutable Global State

```text
Thread A

↓

Global Variable

↑

Thread B

↑

Thread C
```

Problems:

- Race conditions
- Synchronization complexity
- Poor scalability

---

### Better

Prefer:

- Immutable objects
- Stateless services

---

# Anti-pattern 2

## Giant Global Lock

```text
Entire Application

↓

One Lock
```

Everything waits.

Parallelism disappears.

---

### Better

Use fine-grained locking where appropriate, or redesign to reduce shared state.

---

# Anti-pattern 3

## Long Critical Sections

```text
Acquire Lock

↓

REST Call

↓

Database

↓

Kafka

↓

Release Lock
```

Threads wait unnecessarily.

---

### Better

Keep critical sections short.

---

# Anti-pattern 4

## Unbounded Thread Creation

```text
Every Request

↓

New Thread
```

Eventually:

- OutOfMemoryError
- Context switching overhead
- Scheduler saturation

---

### Better

ExecutorService.

Thread pools.

Virtual Threads (when appropriate).

---

# Anti-pattern 5

## Ignoring Timeouts

Waiting forever:

```text
Acquire Lock

↓

Wait Forever
```

System freezes.

---

### Better

Use:

- Lock timeouts
- Request timeouts
- Retry policies

---

# Anti-pattern 6

## Blocking Inside Reactive Pipelines

Reactive chain:

```text
Event Loop

↓

Blocking Database Call
```

Entire event loop stalls.

---

### Better

Use non-blocking APIs or offload blocking work to dedicated worker pools.

---

# Anti-pattern 7

## Excessive Synchronization

Synchronizing every method:

```java
synchronized

everywhere
```

Performance collapses.

---

### Better

Synchronize only true critical sections.

---

# Anti-pattern 8

## Distributed Lock for Everything

Every operation:

```
Distributed Lock
```

Network latency increases.

Availability decreases.

---

### Better

Prefer:

- Idempotency
- Optimistic concurrency
- Message-driven workflows

Distributed locks only when absolutely necessary.

---

# Anti-pattern Summary

| Anti-pattern | Better Practice |
|---------------|----------------|
| Global Mutable State | Immutable Data |
| Giant Lock | Fine-Grained Locking |
| Long Critical Sections | Short Critical Sections |
| Unlimited Threads | Thread Pools / Virtual Threads |
| Infinite Wait | Timeouts |
| Blocking Event Loop | Non-Blocking I/O |
| Over Synchronization | Minimal Synchronization |
| Distributed Lock Everywhere | Better Architecture |

---

# 85. Architecture Review Checklist

Before approving a concurrent system design,

verify the following.

---

## Concurrency Model

- [ ] CPU-bound vs I/O-bound identified?
- [ ] Appropriate execution model selected?
- [ ] Thread pool sizing justified?
- [ ] Blocking operations identified?

---

## Shared State

- [ ] Shared mutable data minimized?
- [ ] Ownership clearly defined?
- [ ] Stateless services preferred?
- [ ] Immutable objects considered?

---

## Synchronization

- [ ] Critical sections minimized?
- [ ] Appropriate lock selected?
- [ ] Lock ordering documented?
- [ ] Timeout strategy defined?

---

## Database

- [ ] Isolation level appropriate?
- [ ] Optimistic/Pessimistic locking justified?
- [ ] Lost update prevention implemented?
- [ ] Transactions kept short?

---

## Distributed Systems

- [ ] Distributed lock truly required?
- [ ] Leader election strategy defined?
- [ ] Idempotency implemented?
- [ ] Failure recovery documented?

---

## Operations

- [ ] Thread pool metrics monitored?
- [ ] Lock contention dashboards available?
- [ ] Thread dump procedures documented?
- [ ] Production alerts configured?

---

# Production Readiness Checklist

```markdown
Concurrency

- [ ] Thread Pool Sized
- [ ] Queue Capacity Configured
- [ ] Timeouts Configured
- [ ] Retry Policy Defined

Synchronization

- [ ] Lock Ordering
- [ ] Deadlock Prevention
- [ ] Contention Monitoring

Database

- [ ] Isolation Level Reviewed
- [ ] Optimistic Locking Evaluated
- [ ] Transactions Short

Distributed

- [ ] Idempotency
- [ ] Distributed Lock Justified
- [ ] Leader Election Tested

Observability

- [ ] Dashboards
- [ ] Alerts
- [ ] Thread Dumps
- [ ] JFR Runbooks
```

---

# 86. Architecture Decision Record (ADR)

## ADR-009

### Title

Adopt Virtual Threads for Request Processing and Thread Pools for CPU-Intensive Tasks

---

### Context

The application processes:

- High-volume REST requests
- Database interactions
- Background report generation
- Image processing

Traditional thread-per-request model consumed excessive memory under peak traffic.

---

### Decision

Adopt:

- Virtual Threads for blocking I/O request handling
- ForkJoinPool for CPU-intensive image processing
- ExecutorService for scheduled background tasks
- CompletableFuture for asynchronous orchestration

---

### Alternatives Considered

1. Traditional fixed thread pools
2. Fully reactive architecture
3. Hybrid concurrency model

---

### Rationale

A hybrid approach provides:

- Simpler programming model for request handling
- Efficient CPU utilization
- Better scalability
- Lower operational complexity than a full reactive rewrite

---

### Consequences

Positive:

- Improved scalability
- Lower memory footprint
- Easier migration from existing code

Negative:

- Multiple concurrency models require developer education
- CPU-bound tasks still require dedicated execution strategies

---

### Success Criteria

- P99 API latency < 250 ms
- Thread pool rejection rate = 0
- No production deadlocks
- Thread utilization < 80% under peak load
- Lock contention below defined SLOs

---

**End of Part 13**

The next section will complete the chapter with:

- **Enterprise Case Studies (Amazon, Google, Netflix, Uber, LinkedIn, Kafka, Kubernetes)**
- **Technology Comparison**
- **Cloud Mapping**
- **Interview Questions (Beginner → Principal Architect)**
- **Revision Cheat Sheet**
- **Chapter Summary**
- **Chapter Completion Checklist**
- **Transition to Chapter 10 – Data Management**
