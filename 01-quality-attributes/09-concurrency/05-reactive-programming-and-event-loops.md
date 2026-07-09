
# 18. Reactive Programming

> **Reactive Programming is a programming paradigm designed to build highly concurrent, asynchronous, non-blocking systems that efficiently utilize hardware resources.**

Unlike traditional applications that dedicate one thread per request,

reactive systems focus on:

- Events
- Data streams
- Non-blocking execution
- Backpressure

Reactive programming has become the foundation of many modern high-performance systems.

Examples:

- Spring WebFlux
- Netty
- Vert.x
- Akka
- Project Reactor

---

# Why Reactive Programming Exists

Traditional web applications use:

```text
Request

↓

One Thread

↓

Wait

↓

Response
```

If the application waits for:

- Database
- REST API
- Kafka
- File System

the thread remains blocked.

Thousands of waiting threads waste memory and CPU.

---

# Example

Suppose:

```
10,000 HTTP Requests
```

Traditional server:

```
10,000 Threads
```

Most threads are simply waiting.

---

Reactive server:

```
200 Threads

↓

10,000 Requests
```

Threads perform useful work instead of waiting.

---

# Traditional Thread-per-Request Model

```text
Request

↓

Assign Thread

↓

Database

↓

Wait

↓

Response

↓

Thread Released
```

During waiting:

CPU remains idle.

---

# Reactive Model

```text
Request

↓

Start Database Call

↓

Release Thread

↓

Database Completes

↓

Callback

↓

Send Response
```

Thread becomes available immediately.

---

# Reactive Principles

Reactive systems emphasize:

- Responsiveness
- Resilience
- Elasticity
- Message-driven communication

These principles originate from the Reactive Manifesto.

---

# Reactive Streams

Reactive programming treats data as a stream.

Example:

```text
Orders

↓

Order 1

↓

Order 2

↓

Order 3

↓

Order 4
```

Instead of waiting for all data,

elements are processed continuously.

---

# Stream Processing

Traditional:

```text
Read All

↓

Process All

↓

Return
```

Reactive:

```text
Read

↓

Process

↓

Read

↓

Process

↓

Read

↓

Process
```

Better memory utilization.

---

# Publisher and Subscriber

Reactive Streams define four key roles.

---

## Publisher

Produces data.

Example:

```text
Kafka

↓

Events
```

---

## Subscriber

Consumes data.

```text
Notification Service

↓

Consumes Events
```

---

## Subscription

Connects publisher and subscriber.

Controls:

- Demand
- Flow

---

## Processor

Acts as both:

Publisher

and

Subscriber.

---

# Reactive Flow

```text
Publisher

↓

Subscription

↓

Processor

↓

Subscriber
```

Each component communicates asynchronously.

---

# Example

Order Service publishes:

```
Order Created
```

Inventory subscribes.

Processes event.

Publishes:

```
Inventory Reserved
```

Payment subscribes.

Workflow progresses naturally.

---

# Non-Blocking Execution

Blocking:

```text
Thread

↓

Database

↓

Wait
```

Thread unavailable.

---

Non-blocking:

```text
Thread

↓

Start Request

↓

Return Thread

↓

Database Responds

↓

Resume
```

CPU serves other work meanwhile.

---

# Benefits

- High concurrency
- Better scalability
- Lower memory usage
- Better CPU utilization
- High throughput

---

# Challenges

Reactive programming introduces:

- Complex debugging
- Learning curve
- Callback chains
- Harder stack traces

Developers must think differently.

---

# 19. Event Loop Architecture

One of the most important concepts in high-performance networking.

---

# What Is an Event Loop?

An Event Loop continuously waits for events,

then dispatches work.

Instead of:

```
One Thread

↓

One Request
```

Event Loop uses:

```
One Thread

↓

Many Connections
```

---

# Simple Event Loop

```text
while(true){

Wait For Event

↓

Handle Event

↓

Continue

}
```

Loop never stops.

---

# Restaurant Analogy

Traditional:

One waiter per customer.

Reactive:

One waiter.

Takes orders.

Moves immediately.

Returns only when food ready.

One waiter manages many tables.

---

# Event Loop Visualization

```text
Connections

↓

Event Queue

↓

Event Loop

↓

Callbacks
```

Thousands of sockets.

Very few threads.

---

# Types of Events

Examples:

- HTTP Request
- Database Response
- Kafka Message
- Timer
- File Ready
- Socket Ready

Everything becomes:

An event.

---

# Callback

Suppose:

Database query finishes.

Instead of:

Waiting,

Event Loop invokes:

```
Callback
```

Application continues processing.

---

# Why Event Loops Scale

Suppose:

```
100,000 Connections
```

Traditional server:

```
100,000 Threads
```

Reactive server:

```
8 Event Loops
```

Huge resource savings.

---

# Event Loop Timeline

```text
Connection 1

↓

Waiting

----------------

Connection 2

↓

Waiting

----------------

Connection 3

↓

Waiting

----------------

Database Ready

↓

Callback

↓

Response
```

Most connections consume almost no CPU while waiting.

---

# Single Event Loop

Many frameworks use:

```
One Event Loop

Per CPU Core
```

Example:

```
8 Cores

↓

8 Event Loops
```

Each loop manages thousands of connections.

---

# Important Rule

Never execute long-running CPU work inside an Event Loop.

Bad:

```text
Event Loop

↓

Image Processing

↓

5 Seconds
```

Everything waits.

---

Better:

```text
Event Loop

↓

Worker Pool

↓

Image Processing

↓

Callback
```

---

# 20. Netty

Netty is one of the most widely used asynchronous networking frameworks in Java.

Many enterprise products are built on Netty.

Examples:

- Spring WebFlux
- gRPC Java
- Elasticsearch
- Cassandra
- Redis clients

---

# Netty Architecture

```text
Client

↓

Socket

↓

Event Loop

↓

Channel

↓

Pipeline

↓

Application
```

---

# Channel

Represents:

Connection.

Each client receives:

One Channel.

---

# Pipeline

Incoming data passes through:

Handlers.

Example:

```text
Decoder

↓

Authentication

↓

Business Logic

↓

Encoder
```

---

# EventLoopGroup

Contains:

Multiple Event Loops.

Usually:

One per CPU core.

---

# Why Netty Is Fast

- Non-blocking sockets
- Zero-copy optimizations
- Minimal thread switching
- Event-driven execution
- Efficient memory management

---

# 21. Spring WebFlux

Spring Boot traditionally uses:

```
Spring MVC
```

Thread-per-request.

---

Spring WebFlux introduces:

Reactive programming.

Built on:

- Project Reactor
- Netty

---

# Spring MVC

```text
Request

↓

Thread

↓

Wait

↓

Response
```

---

# Spring WebFlux

```text
Request

↓

Event Loop

↓

Non-blocking

↓

Response
```

---

# Benefits

- Better scalability
- Lower memory
- High concurrency
- Excellent for I/O workloads

---

# When To Use WebFlux

Good for:

- API Gateway
- Streaming
- Chat
- WebSockets
- High-latency services

---

# When NOT To Use

CPU-intensive workloads.

Example:

- AI inference
- Image rendering
- Encryption

Traditional thread pools may perform better.

---

# 22. Node.js Event Loop

Node.js popularized:

Single-threaded Event Loop.

---

Architecture:

```text
JavaScript

↓

Event Loop

↓

Callbacks

↓

libuv

↓

Operating System
```

---

Important:

Node.js itself is single-threaded for JavaScript execution,

but asynchronous I/O is delegated to the operating system and worker threads managed by **libuv**.

---

# Example

HTTP request.

Database call.

Thread does NOT wait.

Database finishes.

Callback executes.

---

# Why Node.js Handles Many Connections

Example:

```
50,000 Clients
```

Traditional:

```
50,000 Threads
```

Node.js:

```
One Event Loop

+

Async I/O
```

Memory usage remains low.

---

# 23. Producer–Consumer Pattern

One of the most important concurrency patterns.

---

# Problem

Producer creates work.

Consumer processes work.

Producer faster than consumer.

What happens?

---

# Architecture

```text
Producer

↓

Queue

↓

Consumer
```

Queue decouples both sides.

---

# Example

Order Service:

Produces:

```
Order Events
```

Kafka:

Queue.

Inventory:

Consumes.

---

# Benefits

- Decoupling
- Scalability
- Reliability
- Load balancing

---

# Multiple Consumers

```text
Producer

↓

Queue

↓

Consumer A

Consumer B

Consumer C
```

Higher throughput.

---

# 24. Work Queues

Work Queue stores pending tasks.

Workers continuously process tasks.

---

# Example

```text
Email Queue

↓

Worker 1

Worker 2

Worker 3
```

Tasks distributed automatically.

---

# Advantages

- Smooth traffic spikes
- Background processing
- Independent scaling
- Retry support

---

# Technologies

Examples:

- Kafka
- RabbitMQ
- Amazon SQS
- Azure Service Bus
- Google Pub/Sub

---

# 25. Backpressure

> **Backpressure is the mechanism that prevents fast producers from overwhelming slower consumers.**

One of the most critical concepts in reactive systems.

---

# Without Backpressure

Producer:

```
100,000 Events/sec
```

Consumer:

```
10,000 Events/sec
```

Queue grows indefinitely.

Eventually:

- Memory exhausted
- Latency increases
- System crashes

---

# With Backpressure

Consumer tells producer:

```
Slow Down
```

or

```
Send 100 More
```

Producer adapts.

---

# Reactive Streams

Subscriber controls demand.

```text
Request

100 Items

↓

Publisher

↓

Send 100

↓

Wait

↓

Request Next 100
```

Consumer controls speed.

---

# Backpressure Strategies

### Buffer

Store temporarily.

```text
Producer

↓

Buffer

↓

Consumer
```

Good for short bursts.

---

### Drop

Discard excess events.

Useful for:

Metrics.

Telemetry.

---

### Latest

Keep only newest event.

Useful for:

Real-time dashboards.

---

### Block

Producer waits.

Often used when correctness is critical.

---

# Real Examples

Kafka:

Consumer Lag indicates producer is faster.

---

API Gateway:

Rate limiting provides a form of backpressure.

---

TCP:

Receive Window controls sender speed.

---

Reactive Streams:

Subscribers explicitly request data.

---

# Comparison

| Approach | Behavior |
|-----------|----------|
| Buffer | Queue extra work |
| Drop | Lose excess events |
| Latest | Keep newest only |
| Block | Slow producer |

---

# Architect's Perspective

Reactive programming is not merely an optimization.

It represents a **different execution model**.

Traditional servers scale by:

```
More Threads
```

Reactive systems scale by:

```
Fewer Threads

+

Event Loops

+

Non-blocking I/O

+

Backpressure
```

Modern platforms such as:

- Spring WebFlux
- Netty
- Node.js
- Kafka
- Reactive Streams

demonstrate that handling millions of concurrent connections is often **more about efficient waiting than faster computation**.

The next challenge is ensuring these concurrent tasks safely access shared resources—leading us into synchronization, locks, atomic operations, and race conditions.

---

**End of Part 5**

The next section will cover:

- **Shared Resources**
- **Critical Sections**
- **Race Conditions**
- **Thread Safety**
- **Memory Visibility**
- **Happens-Before Relationship**
- **Synchronization Fundamentals**
