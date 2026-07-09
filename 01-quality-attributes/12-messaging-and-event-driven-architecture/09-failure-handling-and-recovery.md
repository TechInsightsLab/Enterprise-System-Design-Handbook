
# 69. Failure Handling in Messaging Systems

Distributed systems are designed with one assumption:

> **Failures are inevitable.**

Messages may be:

- Lost
- Delayed
- Duplicated
- Delivered out of order
- Corrupted
- Blocked

Architects must design systems that continue functioning despite these failures.

---

# Types of Messaging Failures

```text
Messaging Failures

├── Poison Messages
├── Consumer Failure
├── Broker Failure
├── Producer Failure
├── Network Failure
├── Duplicate Delivery
├── Ordering Problems
├── Consumer Lag
├── Backpressure
└── Partition Imbalance
```

Each requires a different recovery strategy.

---

# 70. Poison Messages

> **A Poison Message is a message that repeatedly fails processing and can never be successfully consumed without intervention.**

Unlike temporary failures,

Poison Messages fail:

Every time.

---

# Example

Payment Service expects.

```json
{
  "amount":100
}
```

Received.

```json
{
  "amount":"ABC"
}
```

Consumer crashes.

Retry.

Fails.

Retry.

Fails.

Forever.

---

# Workflow

```text
Consumer

↓

Message

↓

Exception

↓

Retry

↓

Exception

↓

Retry

↓

Exception
```

Infinite loop.

---

# Problems

Poison messages can:

- Block queues
- Increase retry traffic
- Consume resources
- Delay valid messages
- Cause alert storms

---

# Solution

After.

Maximum retries.

Move message.

To.

```
Dead Letter Queue
```

---

# Production Workflow

```text
Main Queue

↓

Retry

↓

Retry

↓

Retry

↓

DLQ
```

Healthy messages continue.

---

# Enterprise Example

Malformed invoice.

Instead of blocking.

```
10 Million

Invoices
```

Move only.

Bad invoice.

To DLQ.

---

# 71. Backpressure

> **Backpressure is a mechanism that prevents producers from overwhelming consumers when message production exceeds processing capacity.**

Imagine.

Producer sends.

```
100,000 Messages/sec
```

Consumer processes.

```
10,000 Messages/sec
```

Queue grows.

Forever.

---

# Architecture

```text
Producer

↓

100K/sec

↓

Broker

↓

10K/sec

↓

Consumer
```

Lag increases.

---

# Symptoms

- Growing queue depth
- Consumer lag
- Memory pressure
- Increased latency
- Eventual failures

---

# Why Backpressure Matters

Without control.

System collapses.

With backpressure.

Producer slows down.

System stabilizes.

---

# Common Strategies

## Slow Down Producers

Producer reduces publishing rate.

---

## Increase Consumers

Scale horizontally.

```text
Consumer1

Consumer2

Consumer3
```

---

## Increase Partitions

More parallel processing.

---

## Buffer Messages

Temporary storage.

Useful.

Only if downstream eventually catches up.

---

## Reject Requests

Sometimes.

Best solution.

Protect system.

---

# Enterprise Example

Ticket Booking.

Concert opens.

Traffic.

```
2 Million Users
```

Backpressure prevents database collapse.

---

# 72. Consumer Lag Recovery

Consumer lag.

Means.

Consumers behind producers.

---

# Recovery Strategies

- Add consumers
- Optimize processing
- Increase partitions
- Scale downstream databases
- Reduce message size

---

# Example

Producer.

```
1 Million Events

Per Minute
```

Consumers.

```
300,000

Per Minute
```

Lag.

Growing.

Need intervention.

---

# Monitoring

Alert on.

- Lag growth
- Processing delay
- Queue depth
- Consumer health

---

# 73. Retry Strategies

Retries recover from.

Temporary failures.

Not permanent failures.

---

# Immediate Retry

```text
Failure

↓

Retry

Immediately
```

Good.

For transient network glitches.

---

# Fixed Delay Retry

```text
Failure

↓

30 Seconds

↓

Retry
```

Useful.

Database restart.

---

# Exponential Backoff

```text
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

Most common.

---

# Exponential Backoff + Jitter

Better.

Random delay.

Added.

```text
8 sec

+

Random

(0-2 sec)
```

Prevents synchronized retry storms.

---

# Retry Policy

Good retry policy defines:

- Maximum attempts
- Delay strategy
- Retryable exceptions
- Non-retryable exceptions

---

# Never Retry

Examples.

- Invalid schema
- Business validation failure
- Missing mandatory fields
- Unauthorized request

These require correction.

Not retries.

---

# Retry Decision Matrix

| Failure | Retry? |
|----------|--------|
| Network Timeout | ✅ |
| Database Restart | ✅ |
| Broker Temporary Failure | ✅ |
| Invalid JSON | ❌ |
| Business Rule Violation | ❌ |
| Authentication Failure | Usually ❌ until credentials are corrected |

---

# 74. Dead Letter Queue Processing

DLQ.

Should not become.

A graveyard.

---

# Proper Workflow

```text
DLQ

↓

Investigation

↓

Root Cause

↓

Fix

↓

Replay

↓

Success
```

---

# DLQ Dashboard

Monitor.

- Message count
- Growth rate
- Failure reason
- Retry attempts
- Oldest message age

---

# Replay

After fixing issue.

Replay.

Messages.

Back.

To.

Original queue.

---

# Replay Caution

Replaying.

```
5 Million Messages
```

Instantly.

May overload.

Consumers.

Replay gradually.

---

# 75. Replay Strategies

Kafka.

Supports replay.

Naturally.

---

# Replay Use Cases

- Bug fixes
- New analytics
- Rebuild projections
- Disaster recovery
- Machine learning

---

# Example

Analytics bug.

Fixed.

Replay.

```
30 Days

Events
```

Dashboard rebuilt.

---

# Replay Architecture

```text
Consumer

↓

Offset Reset

↓

Replay Events

↓

Reprocess
```

---

# Replay Risks

- Duplicate processing
- Long processing time
- High infrastructure usage
- Downstream overload

Design replay carefully.

---

# 76. Message Ordering Problems

Distributed systems.

Do not always preserve.

Global order.

---

# Example

Producer sends.

```
OrderCreated

↓

OrderCancelled
```

Consumer receives.

```
OrderCancelled

↓

OrderCreated
```

Business inconsistency.

---

# Causes

- Multiple partitions
- Network delays
- Retries
- Parallel consumers

---

# Solutions

- Partition by business key
- Sequence numbers
- Version numbers
- Consumer-side validation

---

# Sequence Numbers

Example.

```text
Event 1

↓

Event 2

↓

Event 3
```

Ignore.

Older events.

---

# 77. Partition Imbalance

One partition.

Receives.

```
90%

Traffic
```

Others.

Idle.

---

# Cause

Poor partition key.

---

# Example

Partition Key.

```
Country
```

Most users.

```
India
```

Single partition overloaded.

---

# Better Key

```
Customer ID
```

Better distribution.

---

# Monitoring

Watch.

- Messages per partition
- Throughput
- CPU
- Consumer utilization

---

# Repartitioning

Changing partitions.

May require.

- Data migration
- Producer updates
- Consumer coordination

Plan partition strategy early.

---

# 78. Broker Failure Recovery

Broker.

Crashes.

---

# Architecture

```text
Leader

↓

Crash

↓

Follower

↓

New Leader
```

Automatic recovery.

Through replication.

---

# Recovery Requirements

- Replication
- Leader election
- ISR
- Producer retry
- Consumer recovery

---

# Producer Failure

Producer crashes.

Before sending.

No event.

---

Producer crashes.

After sending.

Before acknowledgement.

Retry.

Possible duplicate.

Idempotent producer helps.

---

# Consumer Failure

Consumer crashes.

Before acknowledgement.

Broker redelivers.

Another consumer.

Processes.

---

# Broker Failure

Replication.

Elect.

New leader.

Continue.

Minimal interruption.

---

# Network Partition

Producer.

Cannot reach broker.

Consumers.

Still processing.

When network recovers.

Retries resume.

---

# End-to-End Failure Recovery

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

Every stage.

Needs.

Recovery strategy.

---

# Failure Matrix

| Failure | Recovery |
|----------|----------|
| Producer Crash | Retry / Idempotence |
| Broker Crash | Replication + Leader Election |
| Consumer Crash | Offset Recovery |
| Network Failure | Retry |
| Poison Message | DLQ |
| Duplicate Delivery | Idempotency |
| Consumer Lag | Scale Consumers |
| Ordering Issue | Partition Key + Sequence |

---

# Production Incident

## Scenario

Black Friday.

Traffic.

```
10×

Normal
```

Consumers.

Cannot keep up.

Lag.

```
0

↓

15 Million
```

Processing delay.

```
2 Seconds

↓

40 Minutes
```

---

# Root Cause

- Too few consumers
- Too few partitions
- Downstream database bottleneck

---

# Resolution

- Add consumers
- Increase partitions (where appropriate)
- Optimize database
- Replay remaining events
- Improve capacity planning

---

# Production Checklist

Before production.

Verify.

- Retry policies
- DLQ configured
- Backpressure tested
- Replay tested
- Monitoring enabled
- Alerts configured
- Consumer lag dashboard
- Disaster recovery documented

---

# Common Mistakes

### Infinite Retries

Bad messages consume resources forever.

Always define retry limits.

---

### Ignoring Consumer Lag

Lag often provides the earliest warning of capacity issues.

---

### Replay Without Throttling

Millions of replayed events can overwhelm downstream systems.

Replay in controlled batches.

---

### Assuming Ordering Across Partitions

Kafka guarantees ordering only within a partition.

Design partition keys accordingly.

---

### Treating DLQ as Storage

DLQ is temporary operational storage.

Investigate and resolve messages promptly.

---

# Architect's Perspective

Failure handling is where messaging architectures prove their value.

Successful systems assume:

- Producers will fail.
- Brokers will restart.
- Consumers will crash.
- Networks will partition.
- Duplicate messages will occur.

Architects should therefore optimize for:

- Recovery
- Observability
- Business correctness
- Operational simplicity

rather than assuming failures can be completely eliminated.

The question is not:

> **"Will failures happen?"**

The correct architectural question is:

> **"How quickly, safely, and automatically can the system recover when failures occur?"**

---

**End of Part 8**

The next part will cover:

- **Messaging Observability**
- **Producer Metrics**
- **Consumer Metrics**
- **Broker Metrics**
- **Capacity Planning**
- **Cost Optimization**
- **Security Best Practices**
- **Production Incidents**
- **Architecture Review Checklist**
- **ADR**
- **Enterprise Case Studies**
