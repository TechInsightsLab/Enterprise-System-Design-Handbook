
# 10. Concurrency vs Parallelism

> **Concurrency and Parallelism are among the most misunderstood concepts in software engineering.**

Many engineers use these terms interchangeably.

They are **not the same**.

Understanding this distinction is fundamental for every architect.

---

# Simple Definitions

## Concurrency

> **Concurrency is the ability to manage multiple tasks by making progress on each task during overlapping time periods.**

Tasks **appear** to run simultaneously.

They may actually execute:

- One after another
- By taking turns
- Through scheduling

---

## Parallelism

> **Parallelism is the simultaneous execution of multiple tasks using multiple CPU cores or processors.**

Tasks literally execute at the same time.

---

# Simple Analogy

Imagine a chef.

---

## Concurrency

One chef.

Cooking:

- Soup
- Pasta
- Rice

The chef continuously switches.

```text
Soup

↓

Pasta

↓

Rice

↓

Soup

↓

Rice

↓

Pasta
```

Everything progresses.

Nothing happens simultaneously.

---

## Parallelism

Three chefs.

```text
Chef A

↓

Soup

----------------

Chef B

↓

Rice

----------------

Chef C

↓

Pasta
```

All cooking occurs simultaneously.

---

# Restaurant Example

Single cashier.

```text
Customer 1

↓

Customer 2

↓

Customer 3
```

Sequential.

---

Single cashier using concurrency.

```text
Take Order

↓

Prepare

↓

Take Another Order

↓

Collect Payment

↓

Serve
```

Customers overlap.

---

Three cashiers.

```text
Cashier A

Customer 1

----------------

Cashier B

Customer 2

----------------

Cashier C

Customer 3
```

Parallel.

---

# Visual Comparison

## Concurrency

```text
Time →

Task A

■■■   ■■■   ■■■

Task B

   ■■■   ■■■

Task C

      ■■■   ■■■
```

Tasks overlap.

Only one may execute at a time.

---

## Parallelism

```text
CPU Core 1

Task A

■■■■■■■■

--------------------

CPU Core 2

Task B

■■■■■■■■

--------------------

CPU Core 3

Task C

■■■■■■■■
```

All execute simultaneously.

---

# Key Difference

Concurrency answers:

> **How can we manage many tasks?**

Parallelism answers:

> **How can we execute many tasks simultaneously?**

---

# Can You Have Concurrency Without Parallelism?

Yes.

Example:

Single CPU core.

```text
Task A

↓

Task B

↓

Task C

↓

Task A

↓

Task C
```

Operating system switches rapidly.

Appears simultaneous.

Only one task actually executes at any instant.

---

# Can You Have Parallelism Without Concurrency?

Technically yes.

Suppose:

Three independent mathematical calculations.

```text
Core 1

↓

Calculation A

----------------

Core 2

↓

Calculation B

----------------

Core 3

↓

Calculation C
```

No shared resources.

No coordination.

Simply parallel execution.

---

# Relationship

```text
Concurrency

↓

Task Management

--------------------

Parallelism

↓

Task Execution
```

Concurrency focuses on:

Coordination.

Parallelism focuses on:

Hardware utilization.

---

# Modern CPUs

Example:

8-Core CPU.

Each core may execute:

Multiple concurrent threads.

Operating system schedules:

Thousands of runnable threads.

Only a subset executes simultaneously.

---

# Example

Suppose:

1000 HTTP requests arrive.

Server has:

```
16 CPU Cores
```

Operating system:

Schedules requests.

Some execute.

Others wait.

Overall system demonstrates:

Concurrency.

Actual simultaneous execution limited by:

CPU cores.

---

# Java Example

Suppose:

```java
ExecutorService pool =
Executors.newFixedThreadPool(10);
```

100 tasks submitted.

Only:

```
10
```

execute simultaneously.

Remaining tasks:

Wait.

Entire system remains concurrent.

---

# Web Server Example

Spring Boot receives:

```
5000 Requests
```

Tomcat thread pool:

```
200 Threads
```

Only:

200

execute concurrently.

Remaining:

Queued.

---

# Microservices Example

```text
Gateway

↓

Order Service

↓

Payment Service

↓

Inventory Service
```

Every service processes many requests concurrently.

Each service may also execute requests in parallel,

depending on CPU resources.

---

# Kafka Example

Suppose:

Topic contains:

```
100 Partitions
```

Consumer Group:

```
20 Consumers
```

Consumers process partitions concurrently.

Within one consumer,

records may execute sequentially.

Across consumers,

parallel execution occurs.

---

# Kubernetes Example

Cluster:

```
100 Nodes
```

Each node:

```
32 CPU Cores
```

Thousands of Pods execute concurrently.

Many Pods also execute in parallel.

---

# Why Architects Care

Concurrency affects:

- Throughput
- Resource utilization
- Scalability
- Synchronization
- Correctness

Parallelism affects:

- CPU efficiency
- Performance
- Hardware utilization

---

# Common Misconceptions

### Myth

Concurrency means multiple CPUs.

**Reality**

Concurrency can exist on a single CPU.

---

### Myth

Parallelism always improves performance.

**Reality**

Synchronization overhead may reduce performance.

---

### Myth

More threads always increase throughput.

**Reality**

Too many threads increase:

- Context switching
- Memory usage
- Scheduling overhead

---

### Myth

Concurrency guarantees better scalability.

**Reality**

Poor synchronization often reduces scalability.

---

# 11. Processes vs Threads

Concurrency begins with understanding the execution model.

Operating systems execute:

```text
Processes

↓

Threads
```

---

# What Is a Process?

A process is:

> **An independent program executing with its own protected memory space and operating system resources.**

Examples:

Running simultaneously:

```text
Chrome

↓

Spotify

↓

VS Code

↓

Slack
```

Each is a separate process.

---

# Process Characteristics

Each process owns:

- Memory
- Heap
- Stack
- File handles
- Network sockets
- Security context

Processes are isolated.

---

# Process Diagram

```text
Process A

├── Memory

├── Heap

├── Stack

└── Resources

--------------------

Process B

├── Memory

├── Heap

├── Stack

└── Resources
```

No direct memory sharing.

---

# Advantages

- Isolation
- Security
- Fault containment

Crash in one process:

Does not usually crash another.

---

# Disadvantages

- Higher memory usage
- Slower communication
- Expensive creation
- Costly context switches

---

# What Is a Thread?

A thread is:

> **The smallest unit of execution within a process.**

Multiple threads share:

- Heap
- Memory
- Open files

Each thread has:

Its own:

- Stack
- Program Counter
- Registers

---

# Thread Diagram

```text
Process

├── Shared Heap

├── Shared Memory

├── Thread A

│     Stack

├── Thread B

│     Stack

├── Thread C

│     Stack
```

---

# Why Threads Exist

Suppose:

Web server.

Without threads:

```text
Request 1

↓

Finished

↓

Request 2

↓

Finished
```

Very slow.

---

With threads:

```text
Thread 1

↓

Request A

----------------

Thread 2

↓

Request B

----------------

Thread 3

↓

Request C
```

Many requests overlap.

---

# Thread Communication

Threads communicate easily.

Shared memory.

Example:

```java
int counter;
```

Accessible by:

Thread A.

Thread B.

Thread C.

Easy.

Dangerous.

---

# Race Condition Risk

Shared variable:

```
Counter
```

Two threads execute:

```java
counter++;
```

Simultaneously.

Unexpected results occur.

Synchronization required.

---

# Process vs Thread

| Process | Thread |
|----------|---------|
| Independent | Lightweight |
| Own Memory | Shared Memory |
| Expensive Creation | Fast Creation |
| Safer | Higher Concurrency |
| Slow IPC | Easy Communication |

---

# When to Use Processes

Good for:

- Isolation
- Independent services
- Separate applications
- Containers
- Microservices

---

# When to Use Threads

Good for:

- Web servers
- Background jobs
- Database connection pools
- Kafka consumers
- Parallel computation

---

# Processes in Microservices

Each microservice typically runs as:

One process.

Example:

```text
Order Service

↓

Java Process

----------------

Payment Service

↓

Java Process

----------------

Inventory Service

↓

Java Process
```

Isolation improves reliability.

---

# Threads Inside Microservices

Inside each service:

```text
Java Process

↓

HTTP Thread Pool

↓

Kafka Consumer Threads

↓

Background Scheduler

↓

Database Threads
```

Thousands of threads may exist.

---

# Architecture View

```text
Cluster

↓

Node

↓

Container

↓

Process

↓

Threads
```

Understanding this hierarchy helps architects reason about performance and resource utilization.

---

# Architect's Perspective

Concurrency begins long before writing synchronized code.

It starts with understanding **how operating systems execute work**.

Architects make design decisions at multiple levels:

- Should workloads run in separate processes or shared threads?
- How many threads should a service create?
- How large should thread pools be?
- When should asynchronous processing replace additional threads?

Choosing the wrong execution model leads to:

- High memory consumption
- Poor CPU utilization
- Thread starvation
- Context switching overhead
- Reduced scalability

Understanding processes and threads is therefore the foundation for every synchronization mechanism discussed later in this chapter.

---

**End of Part 2**

The next section will cover:

- **CPU Scheduling**
- **Context Switching**
- **CPU Cores**
- **Hyper-Threading**
- **Task Scheduling**
- **Thread Pools**
- **Operating System Scheduling Algorithms**
- **How Modern Servers Handle Millions of Concurrent Requests**
