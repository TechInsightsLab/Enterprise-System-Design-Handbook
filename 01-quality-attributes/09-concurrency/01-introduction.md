
# Concurrency

> **Part I – System Design Foundations**

---

# Concurrency

| Attribute | Value |
|-----------|--------|
| **Version** | 2.0 |
| **Part** | Part I – System Design Foundations |
| **Chapter** | 9 |
| **Reading Time** | ~280–340 Minutes |
| **Difficulty** | Advanced |
| **Last Updated** | July 2026 |

---

> [!NOTE]
> **Concurrency is the ability of a system to manage multiple tasks that overlap in time while maintaining correctness, consistency, performance, and resource efficiency.**

Concurrency is one of the **fundamental pillars of modern system design**.

Every distributed system depends on concurrency.

Examples include:

- Thousands of users placing orders simultaneously.
- Millions of Kafka consumers processing messages.
- Hundreds of microservices serving requests concurrently.
- Databases handling thousands of transactions.
- Kubernetes scheduling thousands of containers.
- Cloud platforms serving millions of API requests.

Without proper concurrency control:

- Data becomes inconsistent.
- Race conditions occur.
- Deadlocks appear.
- Throughput drops.
- Systems become unreliable.

---

# Table of Contents

## Part 1 – Foundations

- Learning Objectives
- Prerequisites
- Business Story
- Terminology
- Why Concurrency Matters
- Concurrency vs Parallelism
- Process vs Thread
- CPU Scheduling
- Context Switching

## Part 2 – Core Concepts

- Shared Resources
- Critical Section
- Race Condition
- Mutual Exclusion
- Thread Safety
- Reentrancy
- Memory Visibility
- Happens-Before Relationship

## Part 3 – Synchronization

- Mutex
- Spin Lock
- Read-Write Lock
- Semaphore
- Monitor
- Condition Variables
- Atomic Operations
- Compare-And-Swap (CAS)
- Lock-Free Programming
- Wait-Free Programming

## Part 4 – Deadlocks

- Deadlock
- Livelock
- Starvation
- Priority Inversion
- Deadlock Prevention
- Deadlock Detection
- Deadlock Recovery

## Part 5 – Databases

- Optimistic Locking
- Pessimistic Locking
- MVCC
- Isolation Levels
- Lost Update
- Dirty Read
- Phantom Read

## Part 6 – Distributed Concurrency

- Distributed Locks
- Leader Election
- ZooKeeper
- etcd
- Redis Locks
- Redlock
- Lease-based Locks

## Part 7 – Enterprise

- Java Concurrency
- Spring Boot
- Kafka
- Kubernetes
- AWS
- Azure
- GCP

## Part 8 – Production

- Monitoring
- Observability
- Production Incidents
- Anti-patterns
- Architecture Review Checklist
- ADR

## Part 9 – Interview Preparation

- Beginner Questions
- Intermediate Questions
- Senior Questions
- Principal Architect Questions

## Part 10 – Revision

- Summary
- Cheat Sheet
- References
- GitHub Structure

---

# Learning Objectives

After completing this chapter, you should be able to:

- Explain concurrency using real-world examples.
- Differentiate concurrency from parallelism.
- Understand race conditions and critical sections.
- Select the appropriate synchronization mechanism.
- Design thread-safe applications.
- Explain optimistic and pessimistic locking.
- Design distributed locking systems.
- Prevent deadlocks and starvation.
- Build scalable concurrent systems.
- Answer Staff and Principal Architect interview questions.

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

Basic knowledge of:

- Operating Systems
- Databases
- Java
- Distributed Systems

will be helpful.

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

---

# Next Chapters

- Data Management
- Caching
- Event-Driven Architecture

---

# Terminology

| Term | Definition |
|------|------------|
| Concurrency | Multiple tasks making progress during overlapping time periods |
| Parallelism | Multiple tasks executing simultaneously on multiple CPU cores |
| Process | Independent program with its own memory space |
| Thread | Lightweight execution unit within a process |
| Race Condition | Incorrect behavior caused by concurrent access to shared data |
| Critical Section | Code accessing shared resources |
| Lock | Synchronization mechanism protecting shared resources |
| Atomic Operation | Operation that completes indivisibly |
| Deadlock | Threads waiting indefinitely for one another |
| Thread Safety | Ability to operate correctly under concurrent execution |

---

# Opening Business Story

Imagine an e-commerce platform during a flash sale.

At exactly:

```
10:00:00
```

One laptop remains in stock.

Suddenly:

```text
Customer A

↓

Place Order

----------------

Customer B

↓

Place Order

----------------

Customer C

↓

Place Order
```

All requests arrive almost simultaneously.

Without concurrency control:

Every request reads:

```
Stock = 1
```

Every request proceeds.

System sells:

```
3 Laptops
```

Although only:

```
1 Laptop
```

actually existed.

The business loses money.

Customers become unhappy.

Support teams become overwhelmed.

This is a classic concurrency problem.

---

# Another Example

Bank Account Balance:

```
₹10,000
```

Two ATM withdrawals occur simultaneously.

```text
ATM A

↓

Withdraw ₹7,000

-------------------

ATM B

↓

Withdraw ₹6,000
```

Without proper synchronization:

Both transactions succeed.

Final balance becomes:

```
-₹3,000
```

An impossible business state.

---

# Airline Booking

Only one seat remains.

Two passengers click:

```
Book Seat
```

at the same instant.

Without concurrency control:

Both receive:

```
Booking Confirmed
```

The airline now has:

```
One Seat

↓

Two Passengers
```

---

# Ride Sharing

One nearby driver exists.

Two customers request rides simultaneously.

Both systems assign:

```
Driver #123
```

Driver receives:

Two ride requests.

One customer must eventually be cancelled.

---

# Why Concurrency Matters

Concurrency is not merely a programming concern.

It directly impacts:

- Revenue
- Customer trust
- Data correctness
- Scalability
- Performance
- Availability

Every enterprise application processes concurrent requests.

Examples:

- Banking
- Healthcare
- Retail
- Logistics
- Finance
- Telecommunications

---

# Definition

Concurrency is:

> **The ability of a system to manage multiple independent tasks that overlap in execution while ensuring correctness when accessing shared resources.**

Notice:

The definition says:

```
Overlap
```

It does NOT necessarily mean:

```
Execute at exactly the same time.
```

That distinction becomes important.

---

# Why Concurrency Exists

Modern systems constantly receive requests.

Example:

```text
Users

↓

API Gateway

↓

Microservices

↓

Database
```

Thousands of users interact simultaneously.

The system must:

- Accept requests
- Process requests
- Coordinate shared resources
- Preserve correctness

Sequential processing would severely limit throughput.

---

# Sequential Processing

Imagine a restaurant with one cashier serving one customer at a time.

```text
Customer 1

↓

Finished

↓

Customer 2

↓

Finished

↓

Customer 3
```

Correct.

But slow.

---

# Concurrent Processing

Now imagine:

Multiple cashiers.

```text
Customer 1

↓

Cashier A

----------------

Customer 2

↓

Cashier B

----------------

Customer 3

↓

Cashier C
```

Higher throughput.

Better customer experience.

More complexity.

---

# Shared Resources

Concurrency becomes challenging only when multiple tasks access the same resource.

Examples:

- Database row
- File
- Memory variable
- Inventory record
- Shopping cart
- Bank account
- Cache entry
- Message queue

If resources are independent,

concurrency is easy.

Shared resources create complexity.

---

# Simple Example

Counter:

```
Count = 0
```

Two threads execute:

```java
count++;
```

Developers often assume:

Final value:

```
2
```

Reality:

May become:

```
1
```

because:

```
count++

is NOT

atomic.
```

This is a race condition.

---

# Concurrency vs Correctness

High concurrency without correctness creates:

- Duplicate orders
- Oversold inventory
- Double payments
- Lost updates
- Corrupted data

Correctness must always come before raw throughput.

---

# Business vs Technical View

Business asks:

> "Can multiple customers use the system simultaneously without incorrect outcomes?"

Engineers ask:

> "How do we synchronize access to shared resources?"

Both describe the same problem from different perspectives.

---

# Evolution of Concurrency

```text
Single Thread

↓

Multiple Threads

↓

Multi-Core CPUs

↓

Distributed Systems

↓

Cloud Computing

↓

Massively Parallel Platforms

↓

AI Workloads
```

As systems evolved,

concurrency became unavoidable.

---

# Common Systems Requiring Concurrency

Examples:

- Web Servers
- Application Servers
- Databases
- Kafka Brokers
- Redis
- Kubernetes
- Operating Systems
- Payment Platforms
- Search Engines
- AI Inference Services

Concurrency is everywhere.

---

# The Core Problem

Whenever multiple actors access shared state,

two questions arise:

1. Can they execute simultaneously?
2. If they can, how do we prevent incorrect results?

Every synchronization mechanism introduced later in this chapter exists to answer these two questions.

---

# Architect's Perspective

Junior engineers often associate concurrency with:

- Threads
- Locks
- Java synchronization

Experienced architects view concurrency much more broadly.

Concurrency exists at multiple levels:

- CPU scheduling
- Operating systems
- Programming languages
- Databases
- Distributed systems
- Message brokers
- Cloud infrastructure

Understanding concurrency at every layer enables architects to design systems that are:

- Correct
- Scalable
- Highly available
- High performance
- Resilient

Concurrency is therefore not simply a programming concept—

it is one of the foundational principles of distributed system architecture.

---

**End of Part 1**

The next part will cover:

- **Concurrency vs Parallelism**
- **Processes vs Threads**
- **CPU Scheduling**
- **Context Switching**
- **Multi-Core Processors**
- **Task Scheduling**
- **Operating System Fundamentals for Architects**
