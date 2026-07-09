
# 47. Deadlock

> **A Deadlock is a situation where two or more threads wait indefinitely for each other to release resources, causing the entire system to stop making progress.**

Deadlocks are among the most dangerous concurrency problems because:

- They usually do not produce exceptions.
- They often cannot recover automatically.
- They may affect only a small percentage of requests, making them difficult to detect.

---

# Real Business Story

Imagine a banking system.

Transfer Money:

```
Account A

↓

Account B
```

Thread A locks:

```
Account A
```

Then attempts to lock:

```
Account B
```

At the same time,

Thread B locks:

```
Account B
```

Then attempts to lock:

```
Account A
```

Result:

```text
Thread A

Waiting For B

↑

↓

Thread B

Waiting For A
```

Neither thread proceeds.

Money transfer stops.

---

# Simple Example

Two locks.

```
Lock A

Lock B
```

Thread A

```text
Acquire Lock A

↓

Acquire Lock B
```

Thread B

```text
Acquire Lock B

↓

Acquire Lock A
```

Both wait forever.

---

# Deadlock Visualization

```text
Thread A

↓

Lock A

↓

Waiting

↓

Lock B

-------------------

Thread B

↓

Lock B

↓

Waiting

↓

Lock A
```

Circular dependency.

---

# Four Necessary Conditions

A deadlock can occur only if **all four** of the following conditions exist.

---

## 1. Mutual Exclusion

Resource cannot be shared.

Example:

Only one thread owns:

```
Database Row Lock
```

---

## 2. Hold and Wait

Thread holds one resource

while waiting for another.

---

## 3. No Preemption

Operating system cannot forcibly take the resource.

Only owner releases it.

---

## 4. Circular Wait

Thread A waits for Thread B.

Thread B waits for Thread C.

Thread C waits for Thread A.

Cycle formed.

---

# Coffman Conditions

These four conditions are collectively called:

```
Coffman Deadlock Conditions
```

Break any one.

Deadlock disappears.

---

# Database Deadlock Example

Transaction A

```sql
UPDATE Orders
```

Then:

```sql
UPDATE Inventory
```

Transaction B

```sql
UPDATE Inventory
```

Then:

```sql
UPDATE Orders
```

Deadlock.

Database detects.

Kills one transaction.

---

# Java Example

```text
Thread A

↓

Lock Customer

↓

Lock Order

--------------------

Thread B

↓

Lock Order

↓

Lock Customer
```

Classic deadlock.

---

# Symptoms

Production systems exhibit:

- Requests never complete
- CPU low
- Threads blocked
- Throughput drops
- No obvious exception

---

# Thread Dump

Java thread dump:

```text
Thread-1

BLOCKED

Waiting For Lock

-------------------

Thread-2

BLOCKED

Waiting For Lock
```

JVM may report:

```
Deadlock Found
```

---

# Business Impact

Deadlocks affect:

- Payments
- Banking
- Order Processing
- Airline Booking
- Inventory

Customers experience:

Infinite waiting.

---

# 48. Livelock

> **A Livelock occurs when threads continuously react to one another but make no useful progress.**

Unlike deadlock,

threads are:

Running.

Not blocked.

But:

Nothing completes.

---

# Example

Two people walking.

Both move:

Left.

Both still collide.

Both move:

Right.

Still collide.

Repeat forever.

Nobody progresses.

---

# Software Example

```text
Thread A

↓

Retry

↓

Retry

↓

Retry
```

Thread B

Same.

Both remain active.

No useful work.

---

# Payment Example

Payment Service retries.

Inventory retries.

Each rollback triggers the other.

Continuous compensation loop.

Workflow never finishes.

---

# Difference

Deadlock:

```
Waiting
```

Livelock:

```
Running

But

No Progress
```

---

# Symptoms

- High CPU
- Infinite retries
- Large logs
- No completed transactions

---

# Prevention

- Randomized retry delays
- Exponential backoff
- Retry limits

---

# 49. Starvation

> **Starvation occurs when a thread never receives the resources it needs because other threads continuously take priority.**

Unlike deadlock,

system continues working.

One thread suffers indefinitely.

---

# Example

Priority Scheduling.

High-priority requests arrive continuously.

Low-priority task:

Never executes.

---

# Example

```text
VIP Customer

↓

VIP Customer

↓

VIP Customer

↓

VIP Customer

↓

Normal Customer

(waiting)
```

Normal request starves.

---

# Database Example

Long-running writes.

Read transaction repeatedly postponed.

Never executes.

---

# CPU Scheduling Example

Highest priority thread:

Always selected.

Background task:

Never scheduled.

---

# Prevention

Operating systems often implement:

```
Aging
```

Waiting thread gradually gains priority.

Eventually executes.

---

# Symptoms

- Long waiting time
- Infinite queue delay
- Some users affected
- Others work normally

---

# 50. Priority Inversion

> **Priority Inversion occurs when a high-priority thread waits for a low-priority thread because the low-priority thread owns a required resource.**

---

# Example

Three threads.

High Priority

Medium Priority

Low Priority

---

Low Priority owns:

```
Lock
```

High Priority:

Needs lock.

Blocked.

Medium Priority:

Runs continuously.

Low Priority never resumes.

High Priority waits.

Unexpected inversion.

---

# Timeline

```text
Low Priority

Acquire Lock

↓

Preempted

----------------

High Priority

Needs Lock

↓

Blocked

----------------

Medium Priority

Runs Forever
```

---

# Mars Pathfinder Incident

One of the most famous real-world examples.

NASA's Mars Pathfinder repeatedly rebooted.

Cause:

Priority inversion.

Low-priority task held resource.

High-priority task blocked.

Watchdog restarted system.

Solution:

Priority inheritance.

---

# Priority Inheritance

Operating system temporarily raises:

Low-priority thread.

Allows it to finish.

Release lock.

High-priority thread resumes.

---

# 51. Deadlock Prevention

Prevent deadlock by eliminating one Coffman condition.

---

# Strategy 1

## Lock Ordering

Always acquire locks in:

Same order.

Example:

Always:

```
Customer

↓

Order

↓

Payment
```

Never:

Random order.

Most effective technique.

---

# Strategy 2

## Timeout

Instead of waiting forever.

```text
Acquire Lock

↓

Timeout

↓

Retry
```

Application eventually recovers.

---

# Strategy 3

## Acquire All Locks First

Acquire every required lock before starting work.

If any unavailable:

Release all.

Retry later.

---

# Strategy 4

## Avoid Nested Locks

Bad:

```text
Lock A

↓

Lock B

↓

Lock C
```

Better:

Simpler synchronization.

---

# Strategy 5

## Immutable Objects

No locks.

No deadlocks.

Preferred whenever possible.

---

# Strategy 6

## Message Passing

Instead of shared memory.

Use:

Kafka.

RabbitMQ.

Actor Model.

No shared lock.

---

# 52. Deadlock Detection

Some systems detect deadlocks.

Example:

Databases.

---

# Wait-For Graph

```text
Thread A

↓

Thread B

↓

Thread C

↓

Thread A
```

Cycle detected.

Deadlock confirmed.

---

# Database Detection

Suppose:

Transaction A waits.

Transaction B waits.

Database detects cycle.

Chooses:

Victim.

Rolls back one transaction.

Other continues.

---

# JVM Detection

Useful tools:

```bash
jstack

jcmd

VisualVM

Java Flight Recorder
```

Detect blocked threads.

---

# Monitoring Metrics

Monitor:

- Blocked Threads
- Lock Wait Time
- Lock Contention
- Deadlock Count

---

# 53. Deadlock Recovery

After detection,

system must recover.

---

# Strategy 1

Kill one thread.

---

# Strategy 2

Rollback one transaction.

---

# Strategy 3

Release locks.

Retry later.

---

# Database Example

MySQL:

```
Deadlock Found

Try Restarting Transaction
```

Application retries.

---

# Distributed Systems

Distributed deadlocks are much harder.

Resources span:

- Databases
- Redis
- Kafka
- Microservices

Global detection becomes difficult.

Architectures instead prefer:

- Short-lived locks
- Idempotency
- Compensation
- Timeouts

---

# Production Incident

## Flash Sale Deadlock

Inventory Service:

Locks:

Inventory.

Order Service:

Locks:

Order.

Payment:

Waits.

Thousands of requests blocked.

---

## Root Cause

Inconsistent lock acquisition order.

---

## Solution

All services adopted:

```
Inventory

↓

Order

↓

Payment
```

Single ordering.

Deadlocks disappeared.

---

# Production Incident

## Banking Deadlock

Two transfer requests:

```
A → B

B → A
```

Executed simultaneously.

Each locked one account.

Both waited.

---

## Solution

Always lock accounts by:

```
Account ID

Ascending Order
```

Independent of transfer direction.

---

# Comparison

| Problem | Threads Running | Progress | CPU Usage |
|----------|-----------------|----------|-----------|
| Deadlock | No | No | Low |
| Livelock | Yes | No | High |
| Starvation | Some | Partial | Normal |
| Priority Inversion | Partial | Slow | Normal |

---

# Best Practices

- Keep critical sections short.
- Always acquire locks in a consistent order.
- Prefer immutable data where possible.
- Use timeouts instead of waiting forever.
- Minimize nested locking.
- Prefer message passing over shared mutable state.
- Monitor lock contention in production.
- Design for graceful recovery.

---

# Common Misconceptions

### Myth

Deadlocks only occur in Java.

**Reality**

Deadlocks occur in:

- Databases
- Operating Systems
- Distributed Systems
- Cloud Platforms

---

### Myth

Using more threads eliminates deadlocks.

**Reality**

More threads usually increase the probability.

---

### Myth

Deadlocks always freeze the entire application.

**Reality**

Sometimes only one workflow becomes blocked.

Other requests continue normally.

---

### Myth

Deadlocks are easy to reproduce.

**Reality**

Timing-dependent bugs may occur only under heavy production load.

---

# Architect's Perspective

Deadlocks are **architectural problems**, not merely programming bugs.

Experienced architects minimize deadlock risk by:

- Reducing shared mutable state.
- Designing clear resource ownership.
- Keeping transactions short.
- Using asynchronous communication.
- Avoiding distributed locks unless absolutely necessary.

Modern cloud-native systems increasingly replace long-lived locks with:

- Optimistic concurrency
- Idempotency
- Event-driven workflows
- Compensation mechanisms

The best deadlock is the one that **cannot occur by design**.

---

**End of Part 9**

The next section will cover:

- **Optimistic Locking**
- **Pessimistic Locking**
- **MVCC (Multi-Version Concurrency Control)**
- **Database Isolation Levels**
- **Lost Updates**
- **Dirty Reads**
- **Non-Repeatable Reads**
- **Phantom Reads**
- **Real Database Concurrency Control**
