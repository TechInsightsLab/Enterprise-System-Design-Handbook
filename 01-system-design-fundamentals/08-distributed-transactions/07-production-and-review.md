
# 34. Monitoring Distributed Transactions

> **If you cannot observe a distributed transaction from start to finish, you cannot reliably operate it in production.**

Unlike a local database transaction,

a distributed transaction spans:

- Multiple services
- Multiple databases
- Multiple message brokers
- Multiple regions
- Multiple retries

Traditional monitoring is insufficient.

---

# Monitoring Lifecycle

```text
Request

↓

Local Transaction

↓

Outbox

↓

Message Broker

↓

Consumer

↓

Business Processing

↓

Compensation

↓

Completed
```

Every stage should be observable.

---

# What Should Be Monitored?

Distributed transactions should be monitored at multiple layers.

```text
Business

↓

Application

↓

Messaging

↓

Database

↓

Infrastructure
```

---

# Business Metrics

Business metrics indicate whether customers are affected.

Examples:

- Orders Completed
- Orders Failed
- Payments Refunded
- Inventory Released
- Compensation Executed
- Duplicate Orders
- Duplicate Payments

---

# Application Metrics

Monitor:

- Transaction Duration
- Saga Completion Time
- Retry Count
- Compensation Count
- Failed Transactions
- Pending Transactions

---

# Messaging Metrics

Examples:

- Queue Depth
- Consumer Lag
- Event Processing Time
- Dead Letter Queue Size
- Retry Count
- Duplicate Messages

---

# Database Metrics

Examples:

- Transaction Duration
- Lock Time
- Commit Latency
- Rollback Count
- Outbox Size
- Inbox Growth

---

# Infrastructure Metrics

Examples:

- CPU
- Memory
- Network Latency
- Pod Restarts
- Database Connections
- Kafka Broker Health

---

# Transaction Dashboard

Example

```text
Orders

98.9%

↓

Saga Success

99.97%

↓

Compensation

0.03%

↓

Retries

1.2%

↓

DLQ

2 Messages

↓

Consumer Lag

320 ms
```

---

# Distributed Tracing

One business transaction should produce:

One trace.

Example:

```text
Customer

↓

API Gateway

↓

Order Service

↓

Kafka

↓

Payment

↓

Inventory

↓

Shipping

↓

Notification
```

Every span shares:

```
Trace ID
```

---

# Correlation ID

Every request should receive:

```
Correlation-ID
```

Example:

```
ORD-2026-100245
```

This identifier travels through:

- HTTP
- Kafka
- RabbitMQ
- Database Logs
- Notifications

Entire workflow becomes searchable.

---

# OpenTelemetry

Modern systems increasingly adopt:

```
OpenTelemetry
```

Collects:

- Metrics
- Logs
- Traces

One standard.

Multiple backends.

---

# Typical Stack

```text
Application

↓

OpenTelemetry

↓

Collector

↓

Prometheus

↓

Grafana

↓

Jaeger

↓

AlertManager
```

---

# 35. Alerting

Good alerts focus on:

Customer impact.

Not infrastructure alone.

---

# Useful Alerts

Examples:

- Saga duration > 5 minutes
- Compensation rate > 1%
- Retry rate increasing
- DLQ growth
- Outbox backlog
- Inbox duplication spike
- Workflow timeout
- Payment reconciliation failures

---

# Bad Alerts

Avoid:

- CPU 70%
- Memory 65%

unless they directly affect business workflows.

---

# 36. Production Incidents

Distributed transactions fail in many unexpected ways.

Learning from production incidents improves architecture.

---

# Incident 1 — Duplicate Payment

Customer:

Clicks Pay.

Network timeout.

Customer retries.

Original payment succeeded.

Retry succeeds again.

Customer charged twice.

---

## Root Cause

No idempotency.

---

## Improvement

Added:

- Idempotency Key
- Payment Deduplication
- Reconciliation Job

---

# Incident 2 — Lost Event

Workflow

```text
Order Saved

↓

Kafka Publish Failed
```

Inventory never updated.

---

## Root Cause

Database commit occurred before event publication.

---

## Improvement

Implemented:

Outbox Pattern.

---

# Incident 3 — Infinite Retry Storm

Inventory unavailable.

Consumers retry immediately.

```text
Retry

↓

Retry

↓

Retry

↓

Retry
```

Thousands of requests.

Inventory database overloaded.

---

## Root Cause

No exponential backoff.

---

## Improvement

Added:

- Exponential Backoff
- Circuit Breaker
- DLQ

---

# Incident 4 — Compensation Failure

Workflow

```text
Payment

↓

Inventory

↓

Shipping Failed

↓

Refund Failed
```

Original compensation failed.

Business remained inconsistent.

---

## Lesson

Compensation must also be:

- Retryable
- Idempotent
- Observable

---

# Incident 5 — Workflow Lost

Saga orchestrator crashed.

Workflow state existed only in memory.

Workflow disappeared.

---

## Root Cause

No durable workflow state.

---

## Improvement

Workflow engine introduced:

Temporal.

---

# Lessons Learned

| Incident | Improvement |
|-----------|-------------|
| Duplicate Payment | Idempotency |
| Lost Event | Outbox |
| Retry Storm | Backoff + Circuit Breaker |
| Failed Compensation | Retry + Monitoring |
| Lost Workflow | Durable Workflow Engine |

---

# 37. Common Anti-patterns

> **Most distributed transaction failures result from architectural mistakes—not framework bugs.**

---

# Anti-pattern 1

## Global Database Transaction

```text
Order

↓

Payment

↓

Inventory

↓

Shipping

↓

One Transaction
```

Problems:

- Tight coupling
- Blocking
- Poor scalability

---

### Better

Independent local transactions.

---

# Anti-pattern 2

## REST Call Inside Transaction

```text
BEGIN

↓

Save Database

↓

Call Payment API

↓

Call Inventory API

↓

COMMIT
```

Long-running transaction.

High latency.

---

### Better

Commit first.

Publish event afterward.

---

# Anti-pattern 3

## Retry Without Idempotency

```text
Charge

↓

Timeout

↓

Retry

↓

Duplicate Charge
```

---

### Better

Retry +

Idempotency Key.

---

# Anti-pattern 4

## Infinite Retries

Never-ending retry loops.

Problems:

- Resource exhaustion
- Cascading failures
- Queue backlog

---

### Better

Retry Policy:

- Limited attempts
- Exponential backoff
- Dead Letter Queue

---

# Anti-pattern 5

## Ignoring Compensation

Forward actions implemented.

Reverse actions missing.

Workflow cannot recover.

---

### Better

Every business action should define:

Compensation.

---

# Anti-pattern 6

## Shared Database

Multiple services update:

One database.

Ownership lost.

Coupling increases.

---

### Better

Database per service.

---

# Anti-pattern 7

## No Correlation ID

Logs become impossible to follow.

---

### Better

Generate one:

Correlation ID

per business transaction.

---

# Anti-pattern Summary

| Anti-pattern | Better Practice |
|---------------|----------------|
| Global Transaction | Saga |
| REST Inside Transaction | Local Commit + Events |
| Retry Without Idempotency | Idempotent Retry |
| Infinite Retry | Backoff + DLQ |
| Missing Compensation | Explicit Compensation |
| Shared Database | Database per Service |
| No Traceability | Correlation ID |

---

# 38. Architecture Review Checklist

Before approving a distributed transaction architecture, verify:

---

## Business

- [ ] Business workflow identified?
- [ ] Compensation defined?
- [ ] Duplicate requests handled?
- [ ] Business ownership clear?

---

## Application

- [ ] Local transactions only?
- [ ] Idempotency implemented?
- [ ] Retry policy documented?
- [ ] Correlation ID propagated?

---

## Messaging

- [ ] Outbox implemented?
- [ ] Inbox implemented?
- [ ] DLQ configured?
- [ ] Ordering requirements defined?

---

## Workflow

- [ ] Saga or TCC selected?
- [ ] Compensation tested?
- [ ] Workflow persistence enabled?

---

## Operations

- [ ] Dashboards
- [ ] Alerts
- [ ] Tracing
- [ ] Runbooks
- [ ] Chaos testing

---

# 39. Production Readiness Checklist

```markdown
Transactions

- [ ] Local ACID
- [ ] Business Workflow
- [ ] Compensation

Messaging

- [ ] Outbox
- [ ] Inbox
- [ ] DLQ
- [ ] Retry Policy

Reliability

- [ ] Idempotency
- [ ] Correlation IDs
- [ ] Monitoring
- [ ] Alerts

Recovery

- [ ] Compensation Tested
- [ ] Workflow Recovery
- [ ] Disaster Recovery
```

---

# 40. Architecture Decision Record (ADR)

## ADR-008

### Title

Adopt Saga Pattern with Outbox for Order Processing

---

### Context

The platform consists of:

- Order
- Payment
- Inventory
- Shipping
- Notification

Each service owns an independent database.

Global XA transactions are unsuitable due to scalability and availability requirements.

---

### Decision

Use:

- Saga Pattern (Orchestration)
- Local Transactions
- Outbox Pattern
- Kafka
- Idempotent Consumers
- Compensation Transactions

---

### Alternatives

Two-Phase Commit.

Rejected because:

- High latency
- Blocking
- Tight coupling
- Poor cloud scalability

---

### Consequences

Positive:

- Independent deployment
- Better scalability
- High availability
- Cloud-native architecture

Negative:

- Eventual consistency
- Compensation complexity
- Operational monitoring required

---

### Success Criteria

- Duplicate payments = 0
- Saga success > 99.9%
- Compensation success > 99.99%
- Average workflow completion < 5 seconds
- End-to-end traceability available

---

# Architecture Scorecard

| Evaluation Area | Questions |
|-----------------|-----------|
| Business Correctness | Can every business workflow complete correctly despite failures? |
| Atomicity | Is atomicity achieved through business compensation where necessary? |
| Availability | Can individual service failures be isolated? |
| Scalability | Can services scale independently? |
| Retry Safety | Are retries idempotent? |
| Messaging | Are events reliably published and consumed? |
| Recovery | Can failed workflows resume automatically? |
| Observability | Can operators trace a complete transaction? |
| Operability | Are dashboards, alerts, and runbooks available? |

---

# Architect's Perspective

Distributed transactions should not be judged solely by whether **every step succeeds immediately**.

Instead, architects should ask:

- Can the business recover from partial failures?
- Can workflows resume after crashes?
- Can duplicate requests be handled safely?
- Can operators diagnose failures quickly?
- Can the system eventually reach the correct business outcome?

A well-designed distributed transaction system is one that **expects failures, limits their impact, and provides deterministic recovery paths**.


---

**End of Part 7**

The next section will cover:

- **Enterprise Case Studies (Amazon, Stripe, Uber, Netflix, PayPal, Airbnb)**
- **Architecture Evolution Timeline**
- **Cloud Mapping**
- **Technology Comparisons**
- **Cost Analysis**
- **Common Myths**
- **Interview Questions (Beginner → Principal Architect)**
- **Revision Cheat Sheet**
- **Chapter Summary**
- **Chapter Completion Checklist**
- **Transition to Chapter 9 – Concurrency**
