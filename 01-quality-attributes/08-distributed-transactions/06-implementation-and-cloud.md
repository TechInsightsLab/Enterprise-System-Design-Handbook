
# 26. Spring Boot Implementation

> **Spring Boot does not provide distributed transactions by default because modern microservice architectures intentionally avoid global ACID transactions.**

Instead, Spring Boot encourages:

- Local Transactions
- Event-Driven Architecture
- Saga Pattern
- Outbox Pattern
- Idempotency
- Retry Mechanisms

---

# Local Transaction

Each service owns:

```text
Spring Boot Service

↓

One Database

↓

@Transactional
```

Example:

```java
@Transactional
public Order createOrder(CreateOrderRequest request) {
    ...
}
```

This transaction should never span:

- Payment Service
- Inventory Service
- Shipping Service

---

# Recommended Architecture

```text
Order Service

↓

@Transactional

↓

Save Order

↓

Save Outbox Event

↓

Commit
```

After commit:

```text
Outbox Publisher

↓

Kafka

↓

Other Services
```

---

# Anti-Pattern

Avoid:

```text
Order Service

↓

@Transactional

↓

REST Call

↓

Payment Service

↓

REST Call

↓

Inventory Service
```

Problems:

- Long-running transaction
- Cascading failures
- Blocking
- Tight coupling

---

# Better Design

```text
Order Service

↓

Commit

↓

Publish Event

↓

Payment Service

↓

Commit

↓

Publish Event
```

Every service owns:

Exactly one transaction.

---

# Spring Retry

Retries help recover transient failures.

Typical transient failures:

- Network timeout
- Temporary database issue
- Message broker unavailable

Retries should always be combined with:

```
Idempotency
```

Never retry non-idempotent operations blindly.

---

# Spring Transaction Boundary

Good practice:

```text
HTTP Request

↓

Business Logic

↓

Database

↓

Commit

↓

Publish Event
```

Keep transaction duration short.

---

# 27. Kafka Transactions

Kafka supports transactional messaging.

This is often misunderstood.

---

# What Kafka Guarantees

Kafka can guarantee:

- Atomic writes across multiple partitions
- Atomic offset commits
- Exactly-once processing semantics (within Kafka's defined boundaries)

It **does not** guarantee global business transactions across databases and external systems.

---

# Kafka Transaction Flow

```text
Producer

↓

Begin Transaction

↓

Send Event A

↓

Send Event B

↓

Commit Transaction
```

Either:

All messages become visible.

Or:

None become visible.

---

# Producer Configuration

```text
Producer

↓

Transactional ID

↓

Begin Transaction

↓

Send

↓

Commit
```

---

# Idempotent Producer

Kafka supports:

```
enable.idempotence=true
```

Benefits:

- No duplicate writes during retries
- Safe producer retries

---

# Consumer Side

Consumers can read:

```
Committed Only
```

avoiding partially completed transactions.

---

# Kafka + Database

Bad architecture:

```text
Database Commit

↓

Kafka Publish
```

Failure:

Database committed.

Kafka failed.

---

Better:

```text
Database

↓

Outbox

↓

Kafka Publisher
```

Outbox guarantees eventual publication.

---

# Exactly Once Reality

Exactly-once processing usually means:

```
Producer

↓

Kafka

↓

Consumer

↓

Idempotent Business Logic
```

Application-level deduplication remains necessary.

---

# 28. RabbitMQ

RabbitMQ differs from Kafka.

---

# Characteristics

RabbitMQ focuses on:

- Reliable messaging
- Flexible routing
- Work queues
- Request-response

---

# Transaction Support

RabbitMQ supports:

- Publisher confirms
- Consumer acknowledgements
- Transactions (rarely used due to performance cost)

Most production systems prefer:

Publisher confirms

instead of:

Broker transactions.

---

# Message Acknowledgement

Workflow:

```text
Consumer

↓

Process

↓

ACK
```

Failure:

```
No ACK

↓

Broker Retries
```

Application must remain idempotent.

---

# Dead Letter Queue

Messages repeatedly failing should move to:

```text
Dead Letter Queue
```

instead of retrying forever.

---

# 29. Redis

Redis frequently participates in distributed workflows.

Examples:

- Distributed locks
- Idempotency keys
- Rate limiting
- Temporary reservations

---

# Distributed Lock

Example:

```text
Service A

↓

Acquire Lock

↓

Process Order

↓

Release Lock
```

Common implementation:

```
SET key value NX PX
```

---

# Redlock

Redis proposes:

```
Redlock
```

for distributed locking across multiple Redis instances.

However,

architects should understand:

Distributed locking is not a substitute for good transaction design.

---

# Idempotency Store

Redis is often used for:

```text
Request ID

↓

Already Exists?

↓

YES

Return Previous Result
```

Very fast.

Low latency.

---

# 30. Kubernetes

Distributed transaction systems commonly run on Kubernetes.

---

# Challenges

Pods may:

- Restart
- Crash
- Scale automatically
- Move to another node

Applications must tolerate this.

---

# Stateless Services

Saga orchestrators should remain:

Stateless.

State belongs in:

- Database
- Workflow Engine
- Durable Storage

---

# Kubernetes Failures

Suppose:

```text
Saga Orchestrator

↓

Pod Crash
```

Recovery:

New pod resumes workflow using persisted state.

---

# Health Checks

Workflow services should expose:

- Liveness
- Readiness
- Startup probes

so Kubernetes can recover unhealthy instances safely.

---

# Horizontal Scaling

Outbox publishers.

Consumers.

Saga workers.

should all support:

Horizontal scaling.

---

# 31. Cloud-Native Implementations

Cloud providers offer managed building blocks for distributed transactions.

---

# AWS

| Requirement | Service |
|-------------|---------|
| Event Bus | EventBridge |
| Queue | SQS |
| Messaging | SNS |
| Streaming | MSK (Kafka) |
| Workflow | Step Functions |
| Functions | Lambda |
| Database | DynamoDB / Aurora |

---

# Example

Order Workflow

```text
API Gateway

↓

Lambda

↓

DynamoDB

↓

EventBridge

↓

Step Functions

↓

Payment

↓

Inventory

↓

Shipping
```

---

# Azure

| Requirement | Service |
|-------------|----------|
| Workflow | Logic Apps |
| Durable Workflow | Durable Functions |
| Messaging | Service Bus |
| Events | Event Grid |
| Streaming | Event Hubs |
| Database | Cosmos DB |

---

# Google Cloud

| Requirement | Service |
|-------------|----------|
| Workflow | Workflows |
| Messaging | Pub/Sub |
| Streaming | Pub/Sub |
| Database | Spanner |
| Functions | Cloud Functions |

---

# Cloud Philosophy

Cloud-native systems prefer:

```
Events

↓

Workflow

↓

Compensation
```

instead of:

```
Global Lock

↓

Global Transaction
```

---

# 32. Workflow Engines

Large organizations rarely implement Saga orchestration manually.

Instead,

they use workflow engines.

---

# Temporal

One of the most popular workflow platforms.

Features:

- Durable execution
- Automatic retries
- Timers
- Compensation
- Long-running workflows

---

# Example

```text
Order Workflow

↓

Payment

↓

Inventory

↓

Shipping

↓

Email
```

If worker crashes,

workflow resumes automatically.

---

# Camunda

BPMN-based workflow engine.

Popular in:

- Banking
- Insurance
- Government

Supports:

- Human approval
- Business processes
- Long-running workflows

---

# Orkes Conductor / Netflix Conductor

Designed for:

Microservice orchestration.

Provides:

- Workflow definition
- Task retries
- Compensation
- Monitoring

---

# Workflow Engine Comparison

| Engine | Best For |
|----------|----------|
| Temporal | Cloud-native microservices |
| Camunda | Business workflows & BPMN |
| Orkes Conductor | Large microservice orchestration |
| AWS Step Functions | AWS-native workflows |
| Azure Logic Apps | Azure integration workflows |
| Google Workflows | GCP-native orchestration |

---

# Build vs Buy

Small systems:

Custom orchestration may be sufficient.

Enterprise platforms:

Workflow engines significantly reduce operational complexity.

---

# 33. Real Production Implementations

## Amazon

Order placement:

- Local transaction
- Event publication
- Inventory reservation
- Payment
- Shipment

Each step independently recoverable.

---

## Uber

Ride booking:

- Driver matching
- Payment authorization
- Ride creation
- Notification

Saga-based workflow.

---

## Netflix

Subscription management:

- Billing
- User account
- Recommendations
- Notifications

Event-driven.

Compensation used instead of global rollback.

---

## Stripe

Payment processing:

- Idempotency keys
- Transaction reconciliation
- Retry safety
- Outbox-like reliability

Focuses on business correctness rather than distributed ACID.

---

## Airbnb

Booking workflow:

- Reservation
- Payment authorization
- Host confirmation
- Notification

Long-running business transaction coordinated through workflow logic.

---

# Technology Comparison

| Technology | Primary Role |
|------------|--------------|
| Spring Boot | Local Transactions |
| Kafka | Event Streaming |
| RabbitMQ | Reliable Messaging |
| Redis | Idempotency & Locks |
| Kubernetes | Platform |
| Temporal | Workflow Orchestration |
| Camunda | BPMN Workflows |
| Step Functions | Managed AWS Workflow |

---

# Common Myths

### Myth

Spring Boot provides distributed transactions automatically.

**Reality**

`@Transactional` manages local transactions only.

---

### Myth

Kafka eliminates the need for Saga.

**Reality**

Kafka transports events. Saga coordinates business workflows.

---

### Myth

Redis distributed locks solve distributed transactions.

**Reality**

Locks prevent concurrent access but do not guarantee business correctness across multiple services.

---

### Myth

Workflow engines replace good architecture.

**Reality**

They automate orchestration, but architects must still design compensation, idempotency, retries, and failure handling.

---

# Architect's Perspective

Frameworks and cloud services **do not solve distributed transactions by themselves**.

Successful enterprise architectures combine:

- Local ACID transactions
- Outbox pattern
- Reliable messaging
- Idempotent processing
- Saga or TCC
- Durable workflow orchestration
- Continuous monitoring

The technology stack may change,

but these architectural principles remain constant.

---

**End of Part 6**

The next section will cover:

- **Monitoring & Observability**
- **Production Incidents**
- **War Stories**
- **Common Anti-patterns**
- **Architecture Review Checklist**
- **Production Readiness Checklist**
- **ADR**
- **Decision Matrix**
- **Architecture Scorecard**
