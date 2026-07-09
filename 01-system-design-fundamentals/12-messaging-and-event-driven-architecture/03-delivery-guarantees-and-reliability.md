
# 10. Message Delivery Guarantees

> **Delivery guarantees define how reliably a messaging system delivers messages from producers to consumers.**

One of the biggest misconceptions is:

> "Message brokers never lose messages."

Reality:

Every distributed messaging system makes trade-offs between:

- Performance
- Availability
- Durability
- Latency
- Consistency

Understanding these trade-offs is essential for architects.

---

# Why Delivery Guarantees Matter

Imagine:

Customer places an order.

```
₹50,000
```

Order message disappears.

Inventory never updated.

Payment succeeds.

Shipment never created.

Business impact:

Severe.

---

# Delivery Models

Most messaging systems provide one of these guarantees:

```text
At-Most-Once

↓

At-Least-Once

↓

Exactly-Once
```

Each solves different business problems.

---

# Trade-off Triangle

Increasing reliability often increases:

- Latency
- Storage
- Network traffic
- Complexity

There is no free lunch.

---

# 11. At-Most-Once Delivery

> **A message is delivered zero or one time.**

If failure occurs,

message may be lost.

But.

Never duplicated.

---

# Workflow

```text
Producer

↓

Broker

↓

Consumer

↓

ACK Lost

↓

Message Lost
```

No retry.

---

# Characteristics

- Fastest
- Lowest overhead
- Possible message loss
- No duplicates

---

# Suitable For

Examples.

- Monitoring metrics
- Log aggregation
- Telemetry
- Non-critical notifications

---

# Not Suitable

- Banking
- Payments
- Orders
- Inventory

Message loss unacceptable.

---

# Real Example

Application logs.

Losing:

One debug log.

Usually acceptable.

---

# Advantages

- Low latency
- Simple implementation
- High throughput

---

# Disadvantages

- Data loss possible
- Business inconsistency
- No recovery

---

# 12. At-Least-Once Delivery

> **Every message is delivered one or more times.**

No message loss.

Duplicates possible.

This is the most common delivery guarantee in enterprise systems.

---

# Workflow

```text
Producer

↓

Broker

↓

Consumer

↓

Processing Success

↓

ACK Lost

↓

Broker Retries

↓

Consumer Processes Again
```

Same message.

Processed twice.

---

# Characteristics

- No message loss
- Duplicate delivery possible
- Reliable
- Most widely adopted

---

# Enterprise Example

Order processing.

Receiving:

Same order twice.

Better than:

Losing order completely.

Duplicates can be handled.

Lost orders are much harder to recover.

---

# Advantages

- Reliable
- Durable
- Business-friendly
- Failure tolerant

---

# Challenges

Consumers must handle:

Duplicate messages.

---

# 13. Exactly-Once Delivery

> **Exactly-Once means the business operation is applied exactly one time, even if messages are retried internally.**

This is the most misunderstood concept in distributed systems.

---

# Common Misconception

Many people believe:

Exactly-Once means:

```
Message delivered

Exactly Once
```

Reality.

Distributed systems cannot always prevent retries.

Instead,

they ensure:

Business effect.

Occurs once.

---

# Practical Meaning

Even if message arrives twice,

consumer processes.

Only once.

---

# Example

Payment.

```
₹500
```

Message received twice.

Customer charged:

Once.

---

# How?

Usually through:

- Idempotency
- Transactions
- Offset management
- Deduplication
- Producer guarantees

---

# Reality

Exactly-Once:

Costs.

More.

- Storage
- CPU
- Network
- Complexity

Only use when business requires it.

---

# Delivery Comparison

| Guarantee | Message Loss | Duplicate Possible |
|------------|--------------|--------------------|
| At-Most-Once | Yes | No |
| At-Least-Once | No | Yes |
| Exactly-Once (Business Effect) | No | Prevented through coordination |

---

# Which Should You Choose?

| Business Scenario | Recommendation |
|-------------------|---------------|
| Metrics | At-Most-Once |
| Email | At-Least-Once |
| Order Processing | At-Least-Once + Idempotency |
| Payments | Exactly-once business effect (typically implemented using idempotency and transactional coordination) |
| Banking | Exactly-once business effect where required |

---

# 14. Message Acknowledgement (ACK)

> **An acknowledgement (ACK) is a confirmation from the consumer indicating that a message has been processed successfully.**

Without ACK,

broker cannot know.

Whether processing succeeded.

---

# Workflow

```text
Broker

↓

Consumer

↓

Process

↓

ACK

↓

Remove Message
```

---

# What If ACK Never Arrives?

Broker assumes.

Consumer failed.

Retries message.

---

# Positive ACK

Consumer says.

```
Success
```

Broker removes message.

---

# Negative ACK (NACK)

Consumer says.

```
Failed
```

Broker may:

- Retry
- Delay
- Send to DLQ

---

# Auto ACK

Broker removes message.

Immediately after delivery.

---

Problem.

Consumer crashes.

Message lost.

---

# Manual ACK

Consumer acknowledges.

After successful processing.

Preferred for critical workloads.

---

# Comparison

| Auto ACK | Manual ACK |
|-----------|------------|
| Faster | Safer |
| Risk of message loss | Higher reliability |
| Simple | More control |

---

# 15. Message Offsets

Offsets are primarily associated with log-based messaging systems such as Kafka.

> **An offset uniquely identifies the position of a message within a partition.**

---

# Example

```text
Partition

↓

Offset 0

↓

Offset 1

↓

Offset 2

↓

Offset 3
```

Every message.

Unique position.

---

# Why Offsets Matter

Consumer crashes.

Last processed.

```
Offset

150
```

Restart.

Continue.

```
151
```

No need to restart from beginning.

---

# Offset Commit

Consumer processes message.

Commits offset.

Broker records progress.

---

# Commit Timing

Two approaches.

---

## Before Processing

```text
Commit

↓

Process
```

Fast.

Risk.

Message lost if processing fails.

---

## After Processing

```text
Process

↓

Commit
```

Preferred.

Duplicate processing possible.

But.

No message loss.

---

# Architect Recommendation

For business-critical systems,

commit offsets only after successful processing and persistence.

---

# 16. Idempotency

> **Idempotency means performing the same operation multiple times produces the same final business result.**

This is one of the most important concepts in distributed systems.

---

# Example

Order Created.

Message delivered twice.

Without idempotency.

Database.

```text
Order

100

Order

100
```

Duplicate.

---

With idempotency.

```text
Order

100
```

One record.

---

# Real Example

Payment.

Retry occurs.

Customer charged.

Only once.

---

# Idempotency Key

Each request contains.

```
Transaction-ID

ABC123
```

If same ID appears again.

Ignore duplicate.

---

# Common Sources

- UUID
- Order ID
- Payment ID
- Request ID
- Client-generated idempotency key

---

# Implementation Strategies

### Database Unique Constraint

```sql
UNIQUE(order_id)
```

Prevents duplicates.

---

### Idempotency Table

```text
Request ID

↓

Processed

↓

Timestamp
```

Ignore repeated requests.

---

### Distributed Cache

Store processed IDs.

Redis.

Fast duplicate detection.

Useful when retention requirements are limited.

---

# 17. Duplicate Detection

Duplicates occur because of:

- Retry
- Network timeout
- Consumer restart
- Broker failover
- Producer retry

Architectures should expect duplicates.

Not assume they never happen.

---

# Detection Workflow

```text
Receive Message

↓

Already Processed?

↓

Yes

↓

Ignore

-----------------

No

↓

Process

↓

Record ID
```

---

# Storage Options

- Database
- Redis
- Kafka compacted topic
- Dedicated deduplication store

Choice depends on durability and retention requirements.

---

# 18. Ordering Guarantees

Many systems require.

Messages processed.

In order.

---

Example.

```
Account Created

↓

Address Updated

↓

Account Deleted
```

Wrong order.

Business inconsistency.

---

# Types of Ordering

## Global Ordering

Every message.

Exactly ordered.

Very expensive.

Rare.

---

## Partition Ordering

Messages ordered.

Within one partition.

Common.

Kafka uses this model.

---

## No Ordering

Maximum throughput.

Consumers process independently.

Suitable when order is irrelevant.

---

# Example

Inventory.

```
Reserve

↓

Release

↓

Reserve
```

Out-of-order processing.

Incorrect inventory.

---

# Preserving Order

Use the same partition key.

Example.

```
Order ID
```

All events for one order.

Remain together.

---

# Trade-off

More ordering.

Means.

Less parallelism.

Architects balance:

Correctness.

Throughput.

---

# 19. Producer Reliability

Producer responsibilities include:

- Retry transient failures
- Avoid duplicate creation
- Confirm broker acknowledgement
- Handle broker unavailability
- Preserve ordering when required

---

# Producer Retry

Example.

Broker unavailable.

Retry.

```
1 sec

↓

2 sec

↓

4 sec
```

Exponential backoff.

---

# Retry Caution

Retries without idempotency.

Can create duplicates.

Always design producer retries with duplicate handling in mind.

---

# 20. Consumer Reliability

Consumers should:

- Process idempotently
- Acknowledge after success
- Handle retries
- Support replay
- Avoid data corruption

---

# Failure Scenario

```text
Consumer

↓

Database Updated

↓

Crash

↓

ACK Missing

↓

Retry

↓

Duplicate
```

Without idempotency.

Duplicate business action.

---

# Solution

Business logic.

Must tolerate.

Repeated delivery.

---

# End-to-End Reliable Messaging

```text
Producer

↓

Broker

↓

Consumer

↓

Database

↓

ACK
```

Every step.

Can fail.

Architects design.

Recovery.

For each failure point.

---

# Common Mistakes

### Assuming Exactly-Once Is Free

It requires additional coordination and complexity.

Use only where business value justifies the cost.

---

### Auto ACK for Critical Workloads

Consumer crashes.

Message permanently lost.

Use manual acknowledgements for important business operations.

---

### Ignoring Duplicate Messages

Retries are normal.

Consumers should be idempotent.

---

### Global Ordering Everywhere

Reduces scalability.

Use ordering only where business correctness requires it.

---

### Producer Without Retries

Temporary network failures become permanent business failures.

Retry transient errors with bounded backoff.

---

# Architect's Perspective

Reliable messaging is **not about preventing failures**.

Failures are inevitable.

Reliable messaging is about ensuring the **correct business outcome despite failures**.

Experienced architects design systems assuming:

- Messages may be duplicated.
- Networks may fail.
- Consumers may crash.
- Brokers may restart.
- Retries will occur.

The architectural objective is therefore **business correctness**, not perfect message delivery.

This is why concepts such as:

- Idempotency
- Manual acknowledgements
- Offset management
- Retry strategies
- Ordering guarantees

are fundamental building blocks of every enterprise messaging system.

---

**End of Part 3**

The next part will cover:

- **Event Producers**
- **Event Consumers**
- **Event Brokers**
- **Domain Events**
- **Integration Events**
- **Event Choreography**
- **Event Orchestration**
- **Event Versioning**
- **Schema Evolution**
- **Event Design Best Practices**
