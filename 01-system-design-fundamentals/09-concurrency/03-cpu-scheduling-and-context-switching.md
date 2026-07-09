
# 12. CPU Scheduling

> **CPU Scheduling is the operating system's mechanism for deciding which task gets access to the CPU and for how long.**

Modern servers may have:

- Thousands of threads
- Hundreds of processes
- Dozens of CPU cores

But:

Only a limited number of tasks can execute simultaneously.

The scheduler decides:

> **Who runs next?**

---

# Why CPU Scheduling Exists

Suppose a server has:

```
8 CPU Cores
```

But receives:

```
10,000 HTTP Requests
```

Can all requests execute immediately?

```
No.
```

Only:

```
8 Threads
```

can execute simultaneously.

The remaining requests must wait.

The scheduler continuously decides:

```
Run

↓

Pause

↓

Resume

↓

Switch
```

---

# Scheduling Visualization

```text
Ready Queue

Task A

Task B

Task C

Task D

↓

CPU Scheduler

↓

CPU Core
```

Every runnable task waits inside a queue.

---

# Operating System Scheduler

The scheduler continuously performs:

```text
Choose Next Thread

↓

Assign CPU

↓

Execute

↓

Interrupt

↓

Choose Again
```

This happens thousands of times every second.

---

# Scheduling Goals

A good scheduler tries to maximize:

- CPU Utilization
- Throughput
- Fairness
- Responsiveness

while minimizing:

- Waiting Time
- Context Switching
- Starvation

---

# Example

Imagine four requests.

```text
A

5 ms

B

2 ms

C

8 ms

D

1 ms
```

Question:

Which should execute first?

Different scheduling algorithms produce different results.

---

# Common Scheduling Algorithms

Operating systems implement various scheduling strategies.

---

# First Come First Serve (FCFS)

Simplest algorithm.

Requests execute in arrival order.

```text
A

↓

B

↓

C

↓

D
```

---

## Advantages

- Simple
- Predictable
- Easy implementation

---

## Disadvantages

Long-running tasks block everyone else.

Example:

```text
Long Job

↓

Short Job

↓

Short Job
```

Short jobs wait unnecessarily.

---

# Shortest Job First (SJF)

Scheduler executes:

Shortest task first.

Example:

```text
D

↓

B

↓

A

↓

C
```

Average waiting time improves.

---

## Advantages

- Excellent throughput
- Lower waiting time

---

## Disadvantages

Long tasks may wait indefinitely.

---

# Round Robin

Very common.

Each task receives:

```
Time Slice
```

Example:

```
5 ms
```

Execution:

```text
Task A

↓

5 ms

↓

Task B

↓

5 ms

↓

Task C

↓

5 ms

↓

Task A
```

Everyone progresses.

---

# Advantages

- Fair
- Responsive
- Interactive systems

---

# Disadvantages

Too many context switches.

---

# Priority Scheduling

Each task has:

Priority.

Example:

```text
Critical Payment

Priority 1

----------------

Analytics

Priority 5
```

Higher priority executes first.

---

## Problem

Low-priority tasks may never execute.

This becomes:

```
Starvation
```

We'll discuss this later.

---

# Multi-Level Queue

Tasks grouped by type.

Example:

```text
System Tasks

↓

Interactive

↓

Background Jobs
```

Each queue has:

Different scheduling rules.

---

# Modern Linux Scheduler

Linux uses:

```
Completely Fair Scheduler (CFS)
```

Goal:

Fair CPU allocation.

Instead of:

First Come First Serve,

Linux attempts to ensure:

Every runnable process receives an appropriate share of CPU time.

---

# Scheduling Timeline

```text
Time →

Task A

■■

Task B

■■

Task C

■■

Task A

■■

Task D

■■
```

Tasks continuously rotate.

---

# Why Architects Should Care

Scheduling affects:

- API latency
- Database throughput
- Kafka consumer performance
- JVM responsiveness

Poor scheduling causes:

- Slow response
- High CPU
- Queue growth

---

# 13. Context Switching

> **Context Switching is the process of saving the state of one running task and restoring another so that CPU execution can continue.**

Think of a teacher grading multiple exam papers.

```text
Paper A

↓

Paper B

↓

Paper C

↓

Paper A
```

Every switch requires remembering:

Where work stopped.

---

# What Is Context?

Every running thread has:

- Program Counter
- CPU Registers
- Stack Pointer
- Execution State

Together:

They form the thread's:

```
Context
```

---

# Context Switch Flow

```text
Running Thread

↓

Save State

↓

Scheduler

↓

Load Next Thread

↓

Continue Execution
```

---

# Example

Suppose:

Thread A executing.

CPU timer expires.

Scheduler interrupts.

```text
Save Thread A

↓

Load Thread B

↓

Execute Thread B
```

Later:

Thread A resumes.

Exactly where it stopped.

---

# Context Switch Diagram

```text
CPU

↓

Thread A

↓

Save

↓

Thread B

↓

Save

↓

Thread C
```

The CPU continuously alternates between runnable tasks.

---

# Cost of Context Switching

Context switching is **not free**.

CPU performs:

- Register save
- Register restore
- Stack switch
- Cache invalidation
- Scheduler work

During this period:

No business logic executes.

---

# Example

Suppose:

One context switch:

```
3 µs
```

One million switches:

```
3 Seconds
```

Lost purely to scheduling overhead.

---

# Too Many Threads

Suppose:

CPU:

```
8 Cores
```

Application creates:

```
20,000 Threads
```

Result:

Constant switching.

CPU spends more time:

Scheduling.

Less time:

Executing business logic.

---

# Thread Explosion

```text
Request

↓

New Thread

↓

New Thread

↓

New Thread

↓

20,000 Threads
```

Memory usage grows.

Scheduler overloaded.

Latency increases.

---

# Better Solution

Use:

```
Thread Pool
```

Fixed number of reusable threads.

We'll explore this shortly.

---

# CPU Cache Impact

Modern CPUs contain:

- L1 Cache
- L2 Cache
- L3 Cache

Context switching may invalidate useful cache contents.

Next thread must reload data.

Performance decreases.

---

# Context Switching Example

Without switching:

```text
Thread

■■■■■■■■■■
```

Continuous execution.

---

With frequent switching:

```text
A

■■

B

■■

C

■■

A

■■

D

■■
```

More interruptions.

Lower efficiency.

---

# Voluntary Context Switch

Occurs when thread:

- Sleeps
- Waits for I/O
- Acquires lock
- Waits for network

Example:

```java
Thread.sleep(1000);
```

Scheduler selects another thread.

---

# Involuntary Context Switch

Occurs when:

Operating system preempts thread.

Reasons:

- Time slice expired
- Higher priority task
- Interrupt occurred

---

# Measuring Context Switches

Linux:

```bash
vmstat

pidstat

top

perf
```

Useful metrics:

```
cs

Context Switches/sec
```

Large numbers may indicate excessive thread creation or lock contention.

---

# Architecture Impact

Excessive context switching leads to:

- High CPU usage
- Increased latency
- Lower throughput
- Poor scalability

Adding more threads often makes the system slower.

---

# 14. CPU Cores

A CPU core is an independent execution engine.

Example:

```
8-Core CPU
```

Can execute:

```
8 Threads

Simultaneously
```

(ignoring technologies like Hyper-Threading for the moment).

---

# Single Core

```text
CPU

↓

Task A

↓

Task B

↓

Task C
```

Concurrency.

No parallelism.

---

# Multi-Core

```text
Core 1

↓

Task A

----------------

Core 2

↓

Task B

----------------

Core 3

↓

Task C

----------------

Core 4

↓

Task D
```

Parallel execution.

---

# CPU Utilization

Ideal utilization:

```
70%

to

85%
```

Too low:

Hardware wasted.

Too high:

Latency increases.

Queue grows.

---

# Hyper-Threading (Simultaneous Multithreading)

Modern CPUs often expose:

```
Logical Cores
```

Example:

Physical:

```
8 Cores
```

Logical:

```
16 CPUs
```

Operating system schedules threads across logical processors.

---

# Important Note

Hyper-Threading **does not double performance**.

It improves CPU utilization by allowing a core to execute another thread when one thread is stalled (for example, waiting on memory).

Typical improvement:

```
15–30%
```

depending on workload.

---

# CPU-bound vs I/O-bound Workloads

CPU-bound:

- Image processing
- Video encoding
- Encryption
- AI inference

Need:

More cores.

---

I/O-bound:

- Database access
- HTTP calls
- Kafka
- File operations

Spend significant time waiting.

Concurrency often improves utilization more than adding CPU cores.

---

# Thread Count vs CPU Cores

A common mistake:

```
1000 Threads

↓

8 CPU Cores
```

Performance usually decreases.

Better approach:

Choose thread pool size based on workload.

For CPU-bound work:

```
Approximately

Number of CPU Cores
```

For I/O-bound work:

More threads may be beneficial because many threads spend time waiting.

---

# Architect's Perspective

The operating system scheduler is invisible to most developers,

yet it profoundly influences application performance.

Architects should understand:

- How CPU scheduling works.
- The cost of context switching.
- Why excessive threads reduce performance.
- How CPU cores affect scalability.
- Why CPU-bound and I/O-bound workloads require different execution strategies.

This knowledge forms the foundation for designing efficient thread pools, asynchronous systems, and scalable server applications.

---

**End of Part 3**

The next section will cover:

- **Thread Pools**
- **Executor Framework**
- **Asynchronous Programming**
- **Future & CompletableFuture**
- **Reactive Programming**
- **Event Loops**
- **How Modern Servers Handle Millions of Concurrent Requests**
