
# 30. Memory Visibility

> **One of the most difficult concurrency problems is not race conditions—it is memory visibility.**

Many engineers assume:

```
Variable Updated

↓

Other Threads Immediately See It
```

This assumption is **incorrect**.

Modern CPUs, compilers, and programming languages aggressively optimize memory access.

As a result,

different threads may observe different values of the same variable.

---

# Why Memory Visibility Exists

Modern CPUs are incredibly fast.

Main memory (RAM) is comparatively slow.

Example (Approximate Access Times)

| Storage | Latency |
|----------|----------|
| CPU Register | ~1 ns |
| L1 Cache | ~1 ns |
| L2 Cache | ~3 ns |
| L3 Cache | ~10 ns |
| RAM | ~80–120 ns |
| SSD | ~100 µs |

Notice the difference.

Accessing RAM is **100x slower** than reading from CPU cache.

Therefore CPUs use caches aggressively.

---

# CPU Cache Hierarchy

Modern processors contain multiple cache levels.

```text
                RAM
                 │
          ┌──────┴──────┐
          │             │
       L3 Cache (Shared)
      ┌────┴────┐
      │         │
   Core 1     Core 2
     │           │
   L2 Cache   L2 Cache
     │           │
   L1 Cache   L1 Cache
```

Each CPU core may maintain its own copy of data.

---

# Example

Variable:

```java
boolean shutdown = false;
```

Thread A:

```text
shutdown = true
```

Thread B:

```text
while(!shutdown){

    // Continue Processing

}
```

Expected:

Thread B stops.

Reality:

Thread B may continue forever.

Why?

Because Thread B may repeatedly read its own cached copy.

---

# Memory Visibility Problem

```text
Thread A

↓

Updates Variable

↓

RAM

---------------------

Thread B

↓

Reads Cached Copy

↓

Old Value
```

Both threads access the "same" variable.

But not necessarily the same **memory location** at the same moment.

---

# Real Business Example

Suppose:

Inventory Service updates:

```
Inventory Closed
```

Another worker thread still sees:

```
Inventory Open
```

Orders continue arriving.

Incorrect business behavior.

---

# Symptoms

Memory visibility bugs often produce:

- Infinite loops
- Random failures
- Stale data
- Inconsistent state
- Difficult debugging

---

# Visibility vs Atomicity

These are different concepts.

| Problem | Meaning |
|----------|----------|
| Atomicity | Operation completes indivisibly |
| Visibility | Other threads observe updated value |
| Ordering | Instructions execute in expected order |

A program may have:

Correct atomicity.

Incorrect visibility.

---

# Example

```java
counter++;
```

Problem:

Atomicity.

---

```java
shutdown = true;
```

Problem:

Visibility.

---

# 31. CPU Caches

CPU caches dramatically improve performance.

But they complicate concurrency.

---

# Why CPUs Cache Data

Without cache:

Every variable access requires:

```
RAM
```

Too slow.

Instead:

CPU stores recently accessed data.

```text
RAM

↓

L3

↓

L2

↓

L1

↓

CPU Registers
```

Fast execution.

---

# Cache Example

Thread A

```text
Reads Balance

↓

Stores in L1 Cache
```

Thread B

Updates balance.

Thread A still sees:

Old cached value.

---

# Cache Coherency

Modern CPUs implement:

```
Cache Coherency Protocols
```

Examples:

- MESI
- MOESI

Goal:

Eventually synchronize caches.

Notice:

Eventually.

Not necessarily immediately.

---

# Why Synchronization Exists

Locks.

Volatile variables.

Memory barriers.

All force CPUs to synchronize memory.

---

# 32. Java Memory Model (JMM)

The Java Memory Model defines:

> **How threads interact through memory and what visibility guarantees Java provides.**

Without JMM,

Java programs would behave differently on:

- Intel CPUs
- AMD CPUs
- ARM CPUs

JMM creates:

A consistent programming model.

---

# JMM Architecture

```text
Main Memory

↓

Thread A Working Memory

↓

Thread B Working Memory

↓

Thread C Working Memory
```

Each thread may maintain its own working copy.

---

# Working Memory

Thread executes using:

- Registers
- CPU Cache
- Compiler optimizations

Not necessarily RAM.

---

# Synchronization

Java synchronization ensures:

Working memory updates become visible.

---

# Synchronization Actions

Examples:

- synchronized
- volatile
- Lock
- Atomic classes

These establish visibility guarantees.

---

# 33. Happens-Before Relationship

> **Happens-Before is the most important rule in the Java Memory Model.**

It defines:

When one thread is guaranteed to observe another thread's actions.

---

# Example

Thread A

```java
balance = 100;
ready = true;
```

Thread B

```java
if(ready){

    print(balance);

}
```

Question:

Can Thread B print:

```
0
```

instead of:

```
100
```

Without proper synchronization:

Yes.

---

# Happens-Before Guarantee

If a Happens-Before relationship exists,

Thread B must observe:

```
balance = 100
```

before seeing:

```
ready = true
```

---

# Common Happens-Before Rules

Java guarantees Happens-Before for:

- Lock release → Lock acquire
- volatile write → volatile read
- Thread.start()
- Thread.join()
- Final field initialization
- Executor task submission and completion (through specified concurrency utilities)

---

# Example

```java
synchronized(lock){

    counter++;

}
```

Lock release guarantees:

Next thread sees:

Updated value.

---

# 34. volatile Keyword

> **volatile guarantees memory visibility but does NOT guarantee atomicity.**

One of the most misunderstood Java keywords.

---

# What volatile Does

Suppose:

```java
volatile boolean shutdown;
```

Thread A:

```
shutdown = true;
```

Thread B immediately observes:

```
true
```

Visibility guaranteed.

---

# What volatile Does NOT Do

Example:

```java
volatile int counter;
```

Thread A:

```
counter++;
```

Thread B:

```
counter++;
```

Still unsafe.

Increment remains:

```
Read

↓

Modify

↓

Write
```

Three operations.

Not atomic.

---

# Correct Uses

Excellent for:

- Configuration flags
- Shutdown signals
- Status indicators
- One-time initialization flags

---

# Incorrect Uses

Avoid for:

- Counters
- Banking balances
- Inventory
- Complex state updates

Use:

Atomic classes or synchronization instead.

---

# 35. Instruction Reordering

Modern CPUs and compilers optimize execution.

They may reorder instructions.

---

# Example

Programmer writes:

```java
x = 10;
ready = true;
```

CPU may execute:

```java
ready = true;
x = 10;
```

if it believes:

Behavior remains correct.

Single-threaded:

No issue.

Multi-threaded:

Potential bug.

---

# Example

Thread A

```java
value = 100;

ready = true;
```

Thread B

```java
if(ready){

    print(value);

}
```

Possible output:

```
0
```

Without synchronization,

instruction reordering may expose partially updated state.

---

# Memory Barrier

Synchronization introduces:

```
Memory Barrier
```

Memory barriers prevent unsafe reordering.

Examples:

- synchronized
- volatile
- Lock

---

# Hardware Perspective

Modern CPUs aggressively optimize:

- Out-of-order execution
- Speculative execution
- Register allocation
- Cache optimization

These improve performance,

but require explicit synchronization to preserve correctness across threads.

---

# Summary of Visibility Mechanisms

| Mechanism | Visibility | Atomicity | Ordering |
|------------|------------|-----------|----------|
| Normal Variable | ❌ | ❌ | ❌ |
| volatile | ✅ | ❌ | Partial (visibility + ordering guarantees around volatile accesses) |
| synchronized | ✅ | ✅ | ✅ |
| Lock | ✅ | ✅ | ✅ |
| Atomic Classes | ✅ | ✅ | Limited to atomic operations |

---

# Common Misconceptions

### Myth

Variables automatically stay synchronized across threads.

**Reality**

Threads may cache values independently.

---

### Myth

volatile makes code thread-safe.

**Reality**

It only guarantees visibility.

Atomicity still requires additional synchronization when compound operations are involved.

---

### Myth

Processors execute instructions exactly as written.

**Reality**

Compilers and CPUs may reorder instructions as long as single-threaded semantics are preserved.

---

### Myth

Memory visibility problems only occur on multi-core CPUs.

**Reality**

Compiler optimizations alone can introduce visibility issues, even on a single core with multiple threads.

---

# Architect's Perspective

Most concurrency discussions focus on:

- Locks
- Threads
- Synchronization

Experienced architects also understand:

**Memory semantics.**

A distributed system can have perfectly designed algorithms,

yet still fail if concurrent threads observe stale state.

The Java Memory Model exists to provide predictable behavior across:

- Different CPUs
- Different operating systems
- Different JVM implementations

Before choosing any synchronization mechanism,

architects must understand three independent concerns:

1. **Atomicity** – Can an operation be interrupted?
2. **Visibility** – Will other threads observe the update?
3. **Ordering** – Will operations be observed in the intended sequence?

These concepts form the theoretical foundation for the synchronization mechanisms discussed next.

---

**End of Part 7**

The next section will cover:

- **Mutex**
- **Spin Locks**
- **Read-Write Locks**
- **Semaphores**
- **Monitors**
- **Condition Variables**
- **Atomic Operations**
- **Compare-And-Swap (CAS)**
- **Lock-Free Programming**
- **Wait-Free Programming**
