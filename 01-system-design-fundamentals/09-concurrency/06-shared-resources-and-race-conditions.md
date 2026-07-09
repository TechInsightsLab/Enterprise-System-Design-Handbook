
# 26. Shared Resources

> **Concurrency becomes difficult only when multiple threads or processes access the same resource simultaneously.**

If every thread has its own private data,

there is no concurrency problem.

Problems begin when data is shared.

---

# What Is a Shared Resource?

A shared resource is any resource that can be accessed by multiple execution units simultaneously.

Examples include:

- Memory variables
- Database rows
- Files
- Network sockets
- Cache entries
- Shopping carts
- Inventory records
- Bank accounts
- Kafka partitions
- Redis keys

---

# Example

Suppose:

```text
Inventory

↓

Laptop Stock

↓

Quantity = 5
```

Customer A:

```
Buy Laptop
```

Customer B:

```
Buy Laptop
```

Both access:

```
Quantity
```

at the same time.

Inventory becomes:

A shared resource.

---

# Shared Memory Example

```java
int balance = 10000;
```

Thread A:

```
Withdraw ₹5000
```

Thread B:

```
Withdraw ₹7000
```

Without synchronization,

both threads update:

```
balance
```

Incorrect results may occur.

---

# Examples in Enterprise Systems

| Shared Resource | Example |
|----------------|----------|
| Bank Account | ATM Withdrawals |
| Inventory | Flash Sale |
| Wallet | Payment Processing |
| Coupon | Limited Offers |
| Seat | Flight Booking |
| Cache | Redis |
| Queue | Kafka Partition |
| User Profile | Concurrent Updates |

---

# Shared Resource Architecture

```text
Thread A

↓

Shared Resource

↑

Thread B

↑

Thread C
```

Multiple execution paths.

Single shared state.

---

# Why Shared Resources Are Dangerous

Concurrent access may produce:

- Lost updates
- Duplicate records
- Incorrect balances
- Corrupted memory
- Inconsistent business state

---

# Example

Current Stock:

```
1
```

Two customers read:

```
1
```

Both purchase.

Both update:

```
0
```

Final stock:

```
0
```

Looks correct.

Reality:

Two items sold.

Only one existed.

This is a classic concurrency bug.

---

# Read vs Write

Shared resource operations:

```
Read

↓

Modify

↓

Write
```

This sequence is often **not atomic**.

Another thread may intervene.

---

# Read-Only Resources

Good news.

If all threads only:

```
Read
```

No synchronization required.

Example:

Configuration files.

Reference data.

Static catalogs.

---

# Mutable Resources

Danger begins when threads perform:

```
Read

↓

Modify

↓

Write
```

These operations require coordination.

---

# 27. Critical Section

> **A Critical Section is a section of code that accesses shared resources and therefore must not execute concurrently with conflicting operations.**

Only one thread should execute the critical section at a time (unless the synchronization mechanism allows safe concurrent reads).

---

# Example

```java
balance = balance - amount;
```

Looks simple.

Actually consists of:

```text
Read Balance

↓

Subtract Amount

↓

Write Balance
```

Three operations.

Not one.

---

# Critical Section Diagram

```text
Thread A

↓

Critical Section

↑

Thread B

(waiting)
```

Only one thread enters.

---

# Shopping Example

Inventory:

```
Stock = 5
```

Critical section:

```text
Check Stock

↓

Reduce Stock

↓

Save
```

If two customers execute together,

inventory corruption may occur.

---

# ATM Example

Current Balance:

```
₹10,000
```

Critical section:

```text
Read Balance

↓

Subtract Money

↓

Update Balance
```

Concurrent execution:

Incorrect balance.

---

# Characteristics

Critical sections:

- Access shared data
- Modify state
- Require synchronization
- Should remain as short as possible

---

# Why Keep Critical Sections Small?

Bad:

```text
Acquire Lock

↓

Database

↓

REST Call

↓

Kafka

↓

Release Lock
```

Lock held:

Several seconds.

Other threads blocked.

---

Better:

```text
Acquire Lock

↓

Update Variable

↓

Release Lock
```

Milliseconds.

Higher throughput.

---

# Golden Rule

Never perform:

- Network calls
- File operations
- Long database queries

inside critical sections unless absolutely necessary.

---

# 28. Race Condition

> **A Race Condition occurs when the correctness of a program depends on the unpredictable timing or ordering of concurrent execution.**

Race conditions are among the most common concurrency bugs.

---

# Counter Example

Initial value:

```
Count = 0
```

Two threads execute:

```java
count++;
```

Expected:

```
2
```

Possible result:

```
1
```

---

# Why?

Increment operation:

```text
Read Count

↓

Add One

↓

Write Count
```

Not atomic.

---

# Timeline

```text
Thread A

Read 0

----------------

Thread B

Read 0

----------------

Thread A

Write 1

----------------

Thread B

Write 1
```

Final value:

```
1
```

One increment lost.

---

# Bank Example

Balance:

```
₹1000
```

Thread A:

Withdraw ₹500.

Thread B:

Withdraw ₹700.

Both read:

```
1000
```

Both update.

Possible balance:

```
₹500

or

₹300
```

Correct answer:

Neither.

Business correctness violated.

---

# Flash Sale Example

Inventory:

```
1 Item
```

100 customers click simultaneously.

Without synchronization:

System sells:

```
100 Items
```

Inventory becomes negative.

---

# Common Causes

- Shared mutable state
- Missing synchronization
- Incorrect locking
- Non-atomic operations
- Improper memory visibility

---

# Race Condition Detection

Symptoms:

- Random failures
- Difficult reproduction
- Intermittent bugs
- Different results each run

Often called:

```
Heisenbugs
```

because observing them changes timing.

---

# 29. Thread Safety

> **Thread Safety means a component behaves correctly when accessed concurrently by multiple threads.**

Thread-safe code guarantees:

- Correct results
- No corruption
- No race conditions

regardless of thread scheduling.

---

# Thread-Safe Example

Suppose:

1000 users update:

```
Inventory
```

Final inventory remains correct.

Application is:

Thread-safe.

---

# Thread-Unsafe Example

1000 users update:

```
Inventory
```

Final inventory becomes:

Negative.

Duplicate orders appear.

Thread safety violated.

---

# Characteristics

Thread-safe components:

- Protect shared state
- Synchronize correctly
- Maintain consistency
- Avoid race conditions

---

# Ways to Achieve Thread Safety

Several techniques exist.

---

## Immutable Objects

Immutable data never changes.

Example:

```java
String
```

Safe.

No synchronization required.

---

## Thread Confinement

Each thread owns:

Its own data.

No sharing.

No race condition.

---

## Synchronization

Protect critical sections using:

- Mutex
- Monitor
- Lock

---

## Atomic Operations

Use hardware-supported atomic instructions.

Example:

```
AtomicInteger
```

---

## Concurrent Collections

Java provides:

- ConcurrentHashMap
- CopyOnWriteArrayList
- BlockingQueue

Designed for concurrent access.

---

# Immutable Objects

One of the safest concurrency techniques.

Example:

```text
Configuration

↓

Read Only
```

Thousands of threads.

Zero synchronization.

---

# Mutable Objects

Example:

```text
Shopping Cart

↓

Add Item

↓

Remove Item
```

Synchronization required.

---

# Stateless Services

Microservices often remain:

Stateless.

Every request independent.

Much easier to scale.

---

# Stateful Services

Example:

Session stored in memory.

Multiple threads update session.

Higher synchronization complexity.

---

# Thread Safety in Spring Boot

Controllers are typically:

Singletons.

Shared by every request.

Therefore:

Never store mutable request-specific state in instance variables.

---

Bad:

```java
class OrderController {

    private Order currentOrder;

}
```

Multiple requests share:

```
currentOrder
```

Race condition.

---

Better:

Store request data in:

Method-local variables.

Each request gets its own stack.

---

# Thread Safety Checklist

Ask:

- Is state shared?
- Is state mutable?
- Can two threads modify simultaneously?
- Is synchronization needed?
- Can immutability eliminate the problem?

---

# Architect's Perspective

Concurrency bugs rarely occur because CPUs are fast.

They occur because **multiple execution paths interact with shared mutable state without proper coordination**.

Experienced architects first identify:

1. Shared resources.
2. Critical sections.
3. Ownership boundaries.

Only then do they choose an appropriate synchronization strategy.

A powerful design principle is:

> **Minimize shared mutable state.**

The less data that is shared,

the fewer synchronization problems exist.

Modern cloud-native architectures embrace this principle through:

- Stateless services
- Immutable events
- Database-per-service
- Message-driven communication

These approaches reduce the need for complex synchronization and improve scalability.

---

**End of Part 6**

The next section will cover:

- **Memory Visibility**
- **CPU Caches**
- **Java Memory Model (JMM)**
- **Happens-Before Relationship**
- **volatile Keyword**
- **Instruction Reordering**
- **Synchronization Fundamentals**
- **Why Correct Code Can Still Fail Without Proper Memory Visibility**
