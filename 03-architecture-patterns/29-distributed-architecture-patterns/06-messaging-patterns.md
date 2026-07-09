
# 6. Messaging Patterns

Communication.

Defines.

How services talk.

Messaging.

Defines.

How information.

Flows.

Across.

A distributed system.

---

# Why Messaging Patterns Exist

Imagine.

An order.

Is placed.

What happens next?

```text
Order Created
```

Should.

The Order Service.

Call.

```text
Inventory

↓

Payment

↓

Shipping

↓

Notification

↓

Analytics
```

One by one?

Or.

Should.

It simply.

Publish.

An event.

And let.

Interested services.

React?

Messaging patterns.

Answer.

This question.

---

# Messaging vs Communication

Many engineers.

Confuse.

Communication.

With.

Messaging.

---

Communication.

Answers.

```text
How do services

talk?
```

Examples.

- REST
- gRPC
- HTTP

---

Messaging.

Answers.

```text
How do systems

exchange work?
```

Examples.

- Kafka
- RabbitMQ
- Pulsar
- SQS

---

# Messaging Architecture

```text
Producer

↓

Broker

↓

Consumer
```

Instead of.

```text
Producer

↓

Consumer
```

The broker.

Acts.

As.

An intermediary.

---

# Benefits

Introducing.

A broker.

Provides.

- Decoupling
- Buffering
- Reliability
- Retry
- Fan-out
- Load balancing
- Scalability

---

# Messaging Pattern Classification

```text
Messaging Patterns

│

├── Message Queue

├── Event Bus

├── Publish-Subscribe

├── Competing Consumers

├── Dead Letter Queue

├── Retry Queue

├── Priority Queue

├── Fan-Out

├── Request-Reply

└── Streaming
```

---

# Choosing a Messaging Pattern

Ask.

```text
One Consumer?

↓

Queue

────────────────────

Many Consumers?

↓

Pub/Sub

────────────────────

Failed Messages?

↓

DLQ

────────────────────

Retry Later?

↓

Retry Queue

────────────────────

Urgent Work?

↓

Priority Queue

────────────────────

Need Parallelism?

↓

Competing Consumers
```

---

# 6.1 Message Queue Pattern

The oldest.

Messaging pattern.

---

# Definition

> **A Message Queue delivers each message to exactly one consumer from a group of consumers.**

---

# Architecture

```text
Producer

↓

Queue

↓

Consumer
```

One message.

One consumer.

---

# Example

Customer.

Uploads.

A video.

```text
Upload Service

↓

Video Queue

↓

Video Processor
```

Each video.

Should.

Be processed.

Exactly once.

---

# Characteristics

- Point-to-point
- Work distribution
- Load balancing
- Decoupling

---

# Technologies

- RabbitMQ
- AWS SQS
- Azure Service Bus
- ActiveMQ
- IBM MQ

---

# Advantages

- Simple
- Reliable
- Automatic buffering
- Easy scaling

---

# Disadvantages

- One consumer processes a message
- Not suitable for broadcasting

---

# 6.2 Event Bus Pattern

Sometimes.

Many services.

Need.

The same event.

---

# Definition

> **An Event Bus distributes published events to every interested subscriber.**

---

# Architecture

```text
Producer

↓

Event Bus

↓

Inventory

↓

Shipping

↓

Analytics

↓

Notifications
```

Everyone.

Receives.

The event.

---

# Example

```text
OrderCreated
```

Consumers.

```text
Inventory

Shipping

Fraud Detection

Email

Analytics

Recommendations
```

Producer.

Doesn't know.

Any consumer.

---

# Technologies

- Kafka
- Pulsar
- SNS
- Google Pub/Sub

---

# Queue vs Event Bus

| Queue | Event Bus |
|---------|-----------|
| One consumer | Many consumers |
| Work distribution | Event broadcasting |
| Point-to-point | Publish–Subscribe |
| Commands | Events |

---

# 6.3 Competing Consumers Pattern

Sometimes.

One consumer.

Cannot.

Process.

All messages.

---

# Solution

Multiple consumers.

Compete.

For work.

---

# Architecture

```text
Producer

↓

Queue

↓

Consumer 1

Consumer 2

Consumer 3

Consumer 4
```

Each message.

Is processed.

By.

Exactly one.

Consumer.

---

# Example

Image Processing.

```text
1 Million Images

↓

Image Queue

↓

100 Workers
```

Each worker.

Processes.

Different images.

---

# Benefits

- Horizontal scaling
- Better throughput
- Fault tolerance

---

# Challenges

- Ordering
- Duplicate handling
- Idempotency

---

# 6.4 Dead Letter Queue (DLQ)

Messages.

Sometimes fail.

Repeatedly.

---

Question.

Should.

The system.

Retry forever?

No.

---

# Definition

> **A Dead Letter Queue stores messages that cannot be processed successfully after the configured retry attempts.**

---

# Architecture

```text
Producer

↓

Queue

↓

Consumer

↓

Failure

↓

Retry

↓

Failure

↓

DLQ
```

---

# Business Example

Payment message.

Contains.

Invalid currency.

Retries.

Never succeed.

Eventually.

Message moves.

To.

DLQ.

Operations team.

Investigates.

---

# Why DLQ Matters

Without DLQ.

Poison messages.

Can block.

Entire queues.

---

# Common Reasons

- Invalid data
- Missing dependency
- Corrupt payload
- Business validation failure

---

# Best Practice

DLQ.

Should.

Always.

Be monitored.

Never.

Ignored.

---

# 6.5 Retry Queue Pattern

Not all failures.

Are permanent.

Sometimes.

A dependency.

Is temporarily unavailable.

---

Example.

```text
Payment Service

↓

503 Service Unavailable
```

Retry immediately?

No.

Wait.

Retry later.

---

# Architecture

```text
Main Queue

↓

Consumer

↓

Temporary Failure

↓

Retry Queue

↓

Delay

↓

Main Queue
```

---

# Retry Strategy

Typical delays.

```text
1 min

↓

5 min

↓

15 min

↓

1 hour

↓

DLQ
```

This is called.

**Exponential Backoff.**

---

# Benefits

- Handles transient failures
- Protects downstream systems
- Improves resilience

---

# 6.6 Priority Queue

Not every message.

Has.

Equal importance.

---

Example.

Hospital.

```text
Emergency

↓

Normal

↓

Background
```

Emergency.

Should.

Execute first.

---

# Architecture

```text
Priority Queue

│

├── High

├── Medium

└── Low
```

Consumers.

Always process.

Higher priority.

First.

---

# Business Examples

- Fraud alerts
- Emergency notifications
- VIP customer requests
- Security incidents

---

# Challenge

Poor design.

Can cause.

**Starvation**.

Low-priority messages.

May never execute.

---

# 6.7 Fan-Out Pattern

Need.

To send.

One message.

To.

Many independent consumers.

---

Architecture.

```text
Producer

↓

Exchange

↓

Queue A

Queue B

Queue C

Queue D
```

Each queue.

Receives.

Its own copy.

---

# Example

```text
User Registered
```

Triggers.

- Welcome Email
- Analytics
- Loyalty Program
- CRM
- Marketing

Each.

Independent.

---

# Fan-Out vs Pub/Sub

Fan-Out.

Usually duplicates.

Messages.

Into multiple queues.

Pub/Sub.

Distributes events.

Through topics.

Conceptually.

Very similar.

---

# 6.8 Request–Reply over Messaging

Sometimes.

Need.

A response.

Without using.

HTTP.

---

Architecture.

```text
Client

↓

Request Queue

↓

Service

↓

Reply Queue

↓

Client
```

Common.

In.

Enterprise integration.

---

# Benefits

- Decoupling
- Reliable delivery
- Offline processing

---

# Drawbacks

- More complex correlation
- Higher latency
- Correlation IDs required

---

# 6.9 Streaming Pattern

Traditional queues.

Deliver.

Individual messages.

Streaming.

Treats data.

As.

Continuous events.

---

Architecture.

```text
Producer

↓

Event Stream

↓

Consumers
```

Consumers.

Can process.

Events.

Continuously.

---

# Business Example

Credit Card Transactions.

```text
Transaction Stream

↓

Fraud Detection

↓

Analytics

↓

Dashboard

↓

Machine Learning
```

Everything.

Runs.

In real time.

---

# Technologies

- Kafka
- Apache Pulsar
- Amazon Kinesis
- Redpanda

---

# Queue vs Stream

| Queue | Stream |
|---------|---------|
| Messages removed after consumption | Events retained for configurable time |
| Work distribution | Event log |
| Consumer tracks queue | Consumer tracks offset |
| One-time processing | Replay possible |

---

# Pattern Relationships

Real systems.

Combine.

Many messaging patterns.

```text
Order Service

↓

Outbox

↓

Kafka

↓

Topic

↓

Inventory

↓

Shipping

↓

Notification

↓

Analytics

↓

DLQ

↓

Operations Dashboard
```

---

# Messaging Best Practices

## Keep Messages Immutable

Never modify.

Published messages.

Create.

New events.

Instead.

---

## Include Correlation IDs

Every message.

Should include.

```text
Correlation ID

Trace ID

Request ID
```

Makes debugging.

Possible.

---

## Design Idempotent Consumers

Consumers.

Must safely process.

Duplicate messages.

This is essential.

In distributed systems.

---

## Avoid Large Messages

Prefer.

```text
Order ID

↓

Fetch Details
```

Instead of.

Huge payloads.

Unless.

Event-Carried State.

Is intentionally used.

---

## Monitor Queue Health

Track.

- Queue length
- Consumer lag
- Retry count
- DLQ size
- Processing latency

These metrics.

Reveal.

System health.

---

# Common Misconceptions

### Misconception 1

> Kafka is a Message Queue.

Not exactly.

Kafka is primarily a **distributed event streaming platform** with durable logs and replay capabilities, although it can also support queue-like workloads.

---

### Misconception 2

> DLQ automatically fixes failed messages.

False.

DLQ isolates failures.

Operations teams.

Must investigate.

And decide.

Whether to replay.

Fix.

Or discard.

---

### Misconception 3

> More consumers always increase throughput.

False.

Eventually.

Bottlenecks shift.

To databases.

External APIs.

Or partitions.

---

### Misconception 4

> Messaging guarantees exactly-once processing.

False.

Most brokers.

Guarantee.

At-least-once delivery.

Exactly-once effects.

Require.

Idempotent consumers.

Inbox pattern.

And careful system design.

---

# Interview Questions

### Q1

When should a Queue be used instead of Pub/Sub?

**Answer**

Use a queue when each message should be processed by only one consumer. Use Pub/Sub when multiple independent services need to react to the same event.

---

### Q2

What problem does a Dead Letter Queue solve?

**Answer**

It isolates permanently failing (poison) messages so they do not repeatedly block normal message processing.

---

### Q3

Why are Retry Queues preferred over immediate retries?

**Answer**

They implement delayed retries, reducing pressure on unhealthy downstream services and improving resilience.

---

### Q4

What is the difference between a Queue and a Stream?

**Answer**

A queue distributes work to consumers, whereas a stream maintains an ordered log of events that consumers can replay by tracking offsets.

---

# Architect's Perspective

Messaging patterns transform distributed systems from tightly coupled request chains into resilient, scalable, event-driven platforms.

The most successful architectures do not rely on messaging alone. They combine **Outbox**, **Inbox**, **Competing Consumers**, **Retry Queues**, **DLQs**, and **Streaming** to build systems that continue operating despite failures, retries, and massive traffic spikes.

Understanding these patterns is essential for designing modern, cloud-native systems that process millions of events reliably.

---

**End of Part 6**

The next part will cover **Resilience Patterns**, including:

- Circuit Breaker
- Retry
- Timeout
- Bulkhead
- Fallback
- Health Check
- Rate Limiting
- Load Shedding
- Backpressure
- Graceful Degradation
