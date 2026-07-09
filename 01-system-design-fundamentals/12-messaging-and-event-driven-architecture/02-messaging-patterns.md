
# 1. Point-to-Point (P2P) Messaging

> **Point-to-Point messaging is a communication pattern where one producer sends a message to a queue, and exactly one consumer processes that message.**

This is the simplest messaging pattern.

---

# Real World Analogy

Think of a restaurant kitchen.

A waiter places an order ticket into:

```
Kitchen Queue
```

Several chefs are available.

Eventually,

only **one chef** prepares the order.

The same order is never cooked by all chefs.

---

# Architecture

```text
Producer

↓

Queue

↓

Consumer
```

---

# Multiple Consumers

```text
          Queue

            │

    ┌───────┼────────┐

    │       │        │

Consumer1 Consumer2 Consumer3
```

Only one consumer receives each message.

---

# Example

Customer places an order.

```
Order #100
```

Queue contains:

```text
Order100

Order101

Order102
```

Three workers process them.

```text
Worker1

↓

Order100

----------------

Worker2

↓

Order101

----------------

Worker3

↓

Order102
```

Every order processed once.

---

# Characteristics

- One producer
- One queue
- One message processed once
- Horizontal scalability
- Load balancing

---

# Enterprise Use Cases

- Order Processing
- Invoice Generation
- Video Encoding
- Image Processing
- Background Jobs
- Email Sending
- PDF Generation

---

# Advantages

- Simple
- Reliable
- Natural load balancing
- Easy to scale consumers

---

# Limitations

Only one consumer receives each message.

If multiple services require the same information,

P2P is not appropriate.

---

# 2. Publish / Subscribe (Pub/Sub)

> **Publish/Subscribe is a messaging pattern where one producer publishes an event and multiple subscribers independently receive copies of that event.**

Unlike queues,

messages are **broadcast**.

---

# Real World Analogy

Television.

One broadcaster.

Millions of viewers.

Everyone receives the same broadcast.

---

# Architecture

```text
             Topic

               │

    ┌──────────┼──────────┐

    │          │          │

Subscriber1 Subscriber2 Subscriber3
```

Every subscriber receives:

The same message.

---

# Example

Customer registers.

Publish.

```
CustomerRegistered
```

Subscribers.

- CRM
- Email
- Analytics
- Marketing

Each receives:

Same event.

---

# Workflow

```text
Producer

↓

Topic

↓

Inventory

↓

Analytics

↓

Email

↓

CRM
```

Independent processing.

---

# Benefits

- Loose coupling
- Easy expansion
- Independent deployment
- Parallel processing

---

# Drawbacks

- Duplicate processing is expected
- Harder debugging
- Event ordering considerations
- Event version management

---

# Enterprise Example

Amazon.

```
OrderPlaced
```

Triggers.

- Inventory
- Payment
- Shipping
- Fraud Detection
- Recommendation Engine

None of these services know about each other.

---

# Queue vs Pub/Sub

| Queue | Pub/Sub |
|---------|----------|
| One Consumer | Multiple Consumers |
| Load Balancing | Broadcast |
| Job Processing | Event Distribution |
| Work Queue | Business Events |

---

# 3. Fan-Out Pattern

> **Fan-Out distributes one incoming message to many independent consumers simultaneously.**

It is conceptually similar to Publish/Subscribe,

but emphasizes broadcasting work to many downstream systems.

---

# Architecture

```text
Producer

↓

Exchange

↓

Queue A

Queue B

Queue C

↓

Consumers
```

One incoming message.

Multiple queues.

---

# Example

Image uploaded.

Need.

- Thumbnail
- Virus Scan
- AI Tagging
- Backup

Each service processes independently.

---

# Advantages

- Massive parallelism
- Independent scaling
- Fault isolation

---

# Challenge

Many downstream systems.

Need monitoring.

---

# 4. Competing Consumers Pattern

> **Multiple consumers compete to process messages from the same queue, increasing throughput while ensuring each message is processed only once.**

---

# Architecture

```text
Queue

↓

Worker1

Worker2

Worker3

Worker4
```

Workers compete.

Each message assigned once.

---

# Example

Invoice Generation.

```
1 Million Invoices
```

Instead of.

One worker.

Use.

```
100 Workers
```

Queue distributes work.

---

# Benefits

- Horizontal scaling
- High throughput
- Better resource utilization

---

# Challenges

- Idempotency
- Ordering
- Retry handling

---

# Load Balancing

Queue naturally balances.

```
Worker Busy

↓

Skip

↓

Next Worker
```

Idle workers receive more work.

---

# Enterprise Example

Video Processing.

```text
Upload

↓

Queue

↓

Encoding Workers
```

Workers scale automatically.

---

# 5. Request-Reply Pattern

> **Request-Reply combines asynchronous messaging with synchronous communication by sending a request message and waiting for a correlated response.**

Unlike REST,

communication still occurs through the broker.

---

# Architecture

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

---

# Example

Pricing Engine.

Application asks.

```
Calculate Premium
```

Receives.

```
Premium

₹12,000
```

---

# Correlation ID

How does the client know:

Which reply belongs to which request?

Every request contains.

```
Correlation ID
```

Example.

```
REQ-987654
```

Response carries:

Same ID.

---

# Workflow

```text
Request

ID=100

↓

Service

↓

Response

ID=100
```

Matching guaranteed.

---

# Benefits

- Loose coupling
- Reliable messaging
- Supports long-running operations

---

# Challenges

- Timeouts
- Duplicate replies
- Correlation management

---

# 6. Dead Letter Queue (DLQ)

> **A Dead Letter Queue stores messages that cannot be processed successfully after repeated retry attempts.**

DLQ prevents:

Infinite retries.

---

# Example

Payment Service.

Receives.

Malformed message.

Retries.

```
5 Times
```

Still fails.

Move to:

```
DLQ
```

---

# Architecture

```text
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

# Why DLQ?

Without DLQ.

Bad message.

Blocks queue.

Forever.

---

# Benefits

- Isolates problematic messages
- Protects healthy traffic
- Enables later investigation

---

# Typical Reasons

- Invalid schema
- Missing data
- Business validation failure
- Poison message
- Code defect

---

# Operations Workflow

```text
DLQ

↓

Engineer Investigation

↓

Fix

↓

Replay
```

---

# Never Ignore DLQ

DLQ should be:

- Monitored
- Alerted
- Reviewed
- Replayed or discarded according to business rules

An ever-growing DLQ is a sign of production issues.

---

# 7. Retry Queue

Instead of immediate retry,

move failed messages to:

Retry Queue.

---

# Workflow

```text
Main Queue

↓

Failure

↓

Retry Queue

↓

Wait

↓

Retry

↓

Success

or

DLQ
```

---

# Why Separate Retry?

Immediate retries can worsen outages.

Example.

Database unavailable.

Retrying.

```
100,000 Messages

Immediately
```

Creates:

Retry Storm.

---

# Better

Retry after.

```
30 Seconds

↓

2 Minutes

↓

5 Minutes
```

System recovers.

---

# Exponential Backoff

Common retry strategy.

```
1 sec

↓

2 sec

↓

4 sec

↓

8 sec

↓

16 sec
```

Reduces system pressure.

---

# Retry Best Practices

- Exponential backoff
- Retry limit
- Jitter (random delay)
- DLQ after maximum retries
- Retry only transient failures

---

# 8. Delay Queue

A Delay Queue intentionally postpones message delivery until a specified time.

---

# Example

Reminder.

```
Appointment Tomorrow

↓

Notify

24 Hours Before
```

Message waits.

Delivered later.

---

# Enterprise Uses

- Payment reminders
- Scheduled emails
- Order timeout
- Reservation expiry
- Delayed retries

---

# Workflow

```text
Message

↓

Delay Queue

↓

Time Elapses

↓

Consumer
```

---

# 9. Priority Queue

Priority Queue processes.

Higher priority.

First.

---

# Example

Hospital.

```
Emergency

↓

Routine
```

Emergency treated first.

---

# Architecture

```text
Priority

High

↓

Medium

↓

Low
```

Consumers always process.

Highest priority available.

---

# Enterprise Examples

High Priority.

- Fraud Detection
- Payment Failure
- Security Alerts

Medium.

- Order Processing

Low.

- Analytics
- Reports
- Email Campaigns

---

# Trade-offs

Low-priority messages may experience starvation if high-priority traffic is continuous.

Architects should define fairness policies where appropriate.

---

# Pattern Selection Guide

| Pattern | Best For |
|-----------|----------|
| Point-to-Point | Background jobs |
| Publish/Subscribe | Business events |
| Fan-Out | Broadcast processing |
| Competing Consumers | High throughput processing |
| Request-Reply | Asynchronous request/response |
| DLQ | Failure isolation |
| Retry Queue | Temporary failures |
| Delay Queue | Scheduled processing |
| Priority Queue | Business prioritization |

---

# Choosing the Right Pattern

## Background Processing

Use:

```
Point-to-Point
```

---

## Notify Many Services

Use:

```
Publish/Subscribe
```

---

## Parallel Processing

Use:

```
Fan-Out
```

---

## Millions of Jobs

Use:

```
Competing Consumers
```

---

## Request + Response

Use:

```
Request-Reply
```

---

## Failure Handling

Use:

```
Retry Queue

+

DLQ
```

---

## Scheduled Processing

Use:

```
Delay Queue
```

---

## Business Priorities

Use:

```
Priority Queue
```

---

# Common Mistakes

### Using Pub/Sub for Work Distribution

Every subscriber processes the same message.

If only one worker should process it,

use a queue instead.

---

### Infinite Retries

Messages repeatedly fail.

System never recovers.

Always define retry limits.

---

### No DLQ

Failed messages disappear or endlessly retry.

Both are operational risks.

---

### Priority Everywhere

Making every message "high priority" eliminates the benefit of prioritization.

Priorities should be reserved for genuinely business-critical work.

---

# Architect's Perspective

Messaging patterns solve **different business problems**.

Architects should never begin with:

> "Let's use Kafka."

Instead begin with:

- Is this work or an event?
- Should one consumer process it or many?
- Does the sender need an immediate response?
- Can processing be delayed?
- What happens when processing fails?
- How should retries behave?

The messaging platform comes **after** understanding the communication pattern.

Choosing the correct pattern usually has a greater architectural impact than choosing the messaging technology itself.

---

**End of Part 2**

The next part will cover:

- **Delivery Guarantees**
- **At-Most-Once**
- **At-Least-Once**
- **Exactly-Once (Practical Reality)**
- **Idempotency**
- **Message Ordering**
- **Duplicate Detection**
- **Acknowledgements**
- **Offsets**
- **Producer and Consumer Reliability**
