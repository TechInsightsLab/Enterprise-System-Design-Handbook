
# 15. Thread Pools

> **Creating a new thread for every task is one of the most common performance mistakes in concurrent systems.**

Modern enterprise applications rarely create threads directly.

Instead, they use:

```
Thread Pools
```

A thread pool maintains a fixed or configurable set of reusable threads.

Tasks are assigned to available threads instead of creating new ones.

---

# Why Thread Pools Exist

Imagine an e-commerce website receiving:

```
100,000 Requests
```

Naive approach:

```text
Request

↓

Create Thread

↓

Execute

↓

Destroy Thread
```

For every request.

Problems:

- Thread creation is expensive.
- Memory consumption increases.
- CPU spends time creating threads.
- Context switching increases.

---

# Better Approach

Create:

```
100 Threads
```

Reuse them forever.

```text
Request Queue

↓

Thread Pool

↓

Worker Threads
```

This significantly improves:

- Throughput
- Memory utilization
- CPU efficiency

---

# Restaurant Analogy

Imagine a restaurant.

---

## Without Thread Pool

Every customer hires a new waiter.

```text
Customer

↓

Hire Waiter

↓

Serve

↓

Fire Waiter
```

Ridiculous.

---

## With Thread Pool

Restaurant has:

```
20 Waiters
```

Customers arrive.

Available waiter serves customer.

Waiter becomes available again.

Much more efficient.

---

# Thread Pool Architecture

```text
Incoming Tasks

↓

Task Queue

↓

Thread Pool

↓

Worker Threads

↓

Completed Tasks
```

Workers continuously process queued tasks.

---

# Internal Working

Suppose:

Pool Size:

```
5
```

Tasks:

```
20
```

Execution:

```text
Tasks

↓

Queue

↓

Thread 1

Thread 2

Thread 3

Thread 4

Thread 5
```

Remaining tasks wait until a worker becomes available.

---

# Benefits

## Reuse

Threads are reused.

Creation cost paid only once.

---

## Better Performance

Less:

- Thread creation
- Thread destruction
- Context switching

---

## Controlled Resource Usage

Instead of:

```
10000 Threads
```

Application maintains:

```
100 Threads
```

Memory becomes predictable.

---

## Improved Stability

Large thread counts often cause:

- OutOfMemoryError
- CPU starvation
- Thread scheduling overhead

Thread pools prevent this.

---

# Thread Pool Components

Typical pool contains:

```text
Task Queue

↓

Worker Threads

↓

Scheduler

↓

Rejected Task Handler
```

---

# Queue

Tasks waiting for execution.

Example:

```text
Task A

Task B

Task C
```

---

# Worker Thread

Continuously executes:

```text
Take Task

↓

Execute

↓

Take Next Task

↓

Execute
```

Workers never terminate under normal operation.

---

# Pool Size

One of the most important tuning parameters.

Too small:

```
Queue grows.
```

Too large:

```
Context switching increases.
```

Finding the right balance is essential.

---

# Fixed Thread Pool

Example:

```
20 Threads
```

Always:

```
20
```

Predictable.

Suitable for:

Server applications.

---

# Cached Thread Pool

Creates threads dynamically.

```text
Low Load

↓

Few Threads

----------------

High Load

↓

Many Threads
```

Can become dangerous under extreme traffic.

---

# Scheduled Thread Pool

Executes tasks:

- Every minute
- Every hour
- After delay

Examples:

- Cleanup jobs
- Cache refresh
- Health checks

---

# Single Thread Executor

Exactly:

```
1 Thread
```

Tasks execute sequentially.

Useful when ordering is critical.

---

# Java Executor Framework

Java introduced:

```java
ExecutorService
```

instead of manually managing threads.

Common implementations:

```java
Executors.newFixedThreadPool()

Executors.newCachedThreadPool()

Executors.newScheduledThreadPool()

Executors.newSingleThreadExecutor()
```

---

# Why ExecutorService?

Instead of:

```java
new Thread(...)
```

developers submit:

```
Task
```

Executor decides:

- Which thread
- When to execute
- Queue management

Cleaner architecture.

---

# Executor Architecture

```text
Application

↓

ExecutorService

↓

Task Queue

↓

Worker Threads
```

Developers submit work,

not threads.

---

# Thread Pool Example

Suppose:

```
Pool Size = 3
```

Tasks:

```
Task 1

Task 2

Task 3

Task 4

Task 5
```

Execution:

```text
Thread 1

Task 1

↓

Task 4

----------------

Thread 2

Task 2

↓

Task 5

----------------

Thread 3

Task 3
```

---

# Thread Pool Metrics

Important metrics:

- Active Threads
- Idle Threads
- Queue Length
- Task Completion Rate
- Rejected Tasks

Architects should monitor these continuously.

---

# 16. Choosing the Right Thread Pool Size

There is **no universal thread count**.

The correct size depends on workload characteristics.

---

# CPU-bound Workloads

Examples:

- Compression
- Encryption
- Image Processing
- Machine Learning
- Scientific Computing

Recommendation:

```
Thread Count

≈

CPU Cores
```

Reason:

Extra threads mostly increase context switching.

---

# Example

Machine:

```
8 CPU Cores
```

Recommended:

```
8

or

9 Threads
```

Not:

```
500 Threads
```

---

# I/O-bound Workloads

Examples:

- REST API calls
- Database queries
- Kafka
- Redis
- File systems

Threads spend significant time waiting.

Additional threads improve utilization.

---

# Example

Thread timeline:

```text
Compute

↓

Waiting

↓

Waiting

↓

Compute
```

CPU idle during waits.

Another thread can execute.

---

# Approximate Formula

A commonly used estimation:

```text
Threads ≈

CPU Cores ×

(1 + Wait Time / Compute Time)
```

Example:

```
8 Cores

Wait = 90 ms

Compute = 10 ms

↓

8 × (1 + 90/10)

↓

80 Threads
```

This is a guideline, not a strict rule.

Always validate with performance testing.

---

# Thread Pool Exhaustion

Suppose:

Pool Size:

```
100
```

Incoming requests:

```
10,000
```

Result:

```text
100 Running

↓

9,900 Waiting
```

Queue grows.

Latency increases.

Eventually:

Timeouts occur.

---

# Thread Starvation

Suppose every thread waits for:

```
Database Response
```

Pool becomes full.

New requests cannot execute.

Application appears:

Frozen.

---

# Blocking Calls

Dangerous example:

```text
HTTP Thread

↓

Calls Payment API

↓

Wait

↓

Calls Inventory

↓

Wait

↓

Calls Shipping

↓

Wait
```

Each thread remains blocked.

Large traffic quickly exhausts the pool.

---

# Better Architecture

```text
Request

↓

Async Call

↓

Release Thread

↓

Callback

↓

Continue Processing
```

Far fewer threads required.

---

# 17. Asynchronous Programming

Concurrency does **not** always require more threads.

Sometimes,

the best solution is:

```
Asynchronous Execution
```

---

# Synchronous Flow

```text
Request

↓

Database

↓

Wait

↓

Response
```

Thread blocked.

---

# Asynchronous Flow

```text
Request

↓

Start Database Call

↓

Thread Released

↓

Database Responds

↓

Resume Processing
```

CPU serves other requests while waiting.

---

# Why Async Matters

Modern applications spend much of their time waiting for:

- Databases
- REST APIs
- Kafka
- File systems
- Cloud services

Blocking threads during waits wastes resources.

---

# Async Timeline

Synchronous:

```text
Thread

■■■■■■■■ Waiting ■■■■■■■■
```

Async:

```text
Thread

■■

Released

↓

Other Work

↓

Resume
```

Better CPU utilization.

---

# Future

A Future represents:

```
Result

Available

Later
```

Application continues doing useful work.

---

# CompletableFuture

Java extends Future with:

- Callbacks
- Composition
- Chaining
- Error handling

Enables asynchronous pipelines.

---

# Async Workflow

```text
Order

↓

Payment

↓

Inventory

↓

Shipping

↓

Notification
```

Some operations may execute concurrently,

reducing overall response time.

---

# When Async Helps

Useful for:

- High-latency I/O
- Independent service calls
- Background processing
- Notification systems

---

# When Async Doesn't Help

CPU-bound work.

Example:

Image compression.

Adding asynchronous wrappers does not reduce CPU requirements.

---

# Architect's Perspective

Thread pools are not simply implementation details.

They define:

- Throughput
- Latency
- Resource utilization
- Scalability

A common progression in enterprise systems is:

```text
New Thread

↓

Thread Pool

↓

Async Programming

↓

Reactive Systems

↓

Event-Driven Architecture
```

As traffic grows,

architects gradually shift from blocking thread-per-request models toward asynchronous, non-blocking designs that maximize hardware utilization while maintaining correctness.

---

**End of Part 4**

The next section will cover:

- **Reactive Programming**
- **Event Loop Architecture**
- **Netty**
- **Spring WebFlux**
- **Node.js Event Loop**
- **How Millions of Concurrent Connections Are Handled**
- **Backpressure**
- **Producer–Consumer Pattern**
- **Work Queues**
