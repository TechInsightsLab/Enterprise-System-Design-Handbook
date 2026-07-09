
# 16. Three-Phase Commit (3PC)

> **Three-Phase Commit (3PC) was introduced to reduce one of the biggest weaknesses of Two-Phase Commit: blocking.**

Recall from 2PC:

If the coordinator crashes after the Prepare phase,

participants remain uncertain.

```text
Prepared

↓

Waiting...

↓

Waiting...

↓

Waiting...
```

No participant knows whether to:

- Commit
- Rollback

3PC introduces an additional phase to reduce this uncertainty.

---

# Why 3PC Was Introduced

Suppose:

```text
Coordinator

↓

Prepare

↓

YES

YES

YES
```

Immediately after receiving all votes,

the coordinator crashes.

Participants remain blocked.

3PC attempts to solve this problem.

---

# Three Phases

Unlike 2PC:

```text
Prepare

↓

Pre-Commit

↓

Commit
```

Three distinct phases exist.

---

# Phase 1 — Can Commit?

Coordinator asks:

```
Can you commit?
```

Participants:

- Validate request
- Reserve resources
- Vote

```
YES

or

NO
```

---

# Phase 2 — Pre-Commit

Coordinator sends:

```
Prepare To Commit
```

Participants now know:

```
Commit is very likely.
```

They transition into:

```
Prepared State
```

Unlike 2PC,

they also know:

Coordinator has already decided.

---

# Phase 3 — Commit

Coordinator finally sends:

```
Commit
```

Participants permanently apply changes.

---

# 3PC Timeline

```text
Client

↓

Coordinator

↓

Can Commit?

↓

YES

YES

YES

↓

Pre-Commit

↓

Acknowledged

↓

Commit

↓

Finished
```

---

# What Happens If Coordinator Fails?

Suppose:

Coordinator crashes after:

```
Pre-Commit
```

Participants know:

Commit decision already reached.

Instead of waiting forever,

they may safely continue after timeout.

Blocking greatly reduced.

---

# State Machine

Coordinator

```text
Start

↓

Can Commit

↓

Pre-Commit

↓

Commit

↓

Finished
```

---

Participant

```text
Idle

↓

Prepared

↓

Pre-Committed

↓

Committed
```

---

# Advantages

Compared to 2PC:

- Reduced blocking
- Better recovery
- Faster failure handling

---

# Limitations

3PC still assumes:

Reliable communication.

In real distributed systems:

- Messages delayed
- Packets lost
- Network partitions

Therefore,

3PC is rarely implemented.

---

# Why 3PC Is Rare

Modern cloud systems prefer:

- Saga
- Event-driven architecture
- Compensation

instead of:

Large distributed locking.

---

# 2PC vs 3PC

| Property | 2PC | 3PC |
|----------|-----|------|
| Phases | 2 | 3 |
| Blocking | High | Lower |
| Complexity | Medium | High |
| Network Messages | Fewer | More |
| Production Usage | Limited | Very Rare |

---

# Why Modern Microservices Avoid XA

Most modern architectures intentionally avoid:

```text
Global Transaction

↓

Multiple Services

↓

One Commit
```

---

# Reasons

---

## Independent Deployment

Microservices should deploy independently.

Global transactions tightly couple services.

---

## Long Locks

Suppose:

Payment waits:

Inventory waits:

Shipping waits:

Everything blocks.

Poor scalability.

---

## Different Technologies

Example:

```text
Payment

↓

Oracle

----------------

Inventory

↓

PostgreSQL

----------------

Notification

↓

Kafka

----------------

Analytics

↓

MongoDB
```

One transaction across all systems becomes impractical.

---

## External Services

Example:

```text
Payment Gateway

↓

Bank API

↓

SMS Provider

↓

Shipping Partner
```

External systems cannot participate in XA.

---

## Cloud Native Philosophy

Modern cloud architecture values:

- Loose coupling
- Independent scaling
- Event-driven workflows

Global transactions oppose these principles.

---

# The Shift Toward Business Transactions

Traditional architecture:

```text
Database

↓

Transaction
```

Modern architecture:

```text
Business Workflow

↓

Events

↓

Compensation

↓

Eventually Correct
```

---

# 17. Saga Pattern

> **The Saga Pattern is the most widely adopted distributed transaction pattern in modern microservices.**

Instead of one global transaction,

a Saga consists of:

> **A sequence of local transactions connected through events or commands.**

Each service commits independently.

If a later step fails,

previous work is compensated.

---

# Local Transactions

Example:

```text
Order

↓

Commit

↓

Payment

↓

Commit

↓

Inventory

↓

Commit

↓

Shipping

↓

Commit
```

Each step owns:

Its own transaction.

---

# Compensation

Suppose:

Shipping fails.

Instead of:

```
ROLLBACK
```

Business performs:

```
Cancel Inventory

↓

Refund Payment

↓

Cancel Order
```

Business correctness restored.

---

# Traditional Rollback

```text
BEGIN

↓

Everything

↓

COMMIT
```

Rollback:

Automatic.

---

# Saga Rollback

```text
Order

↓

Payment

↓

Inventory

↓

Shipping ✗

↓

Compensate Inventory

↓

Refund Payment

↓

Cancel Order
```

Different concept.

---

# Example

Customer purchases:

Laptop.

Workflow:

```text
Order Created

↓

Payment Authorized

↓

Inventory Reserved

↓

Shipment Failed
```

Compensation:

```text
Release Inventory

↓

Refund Payment

↓

Cancel Order
```

Customer sees:

```
Order Cancelled

Refund Initiated
```

---

# Characteristics

Saga provides:

- High availability
- Independent services
- Better scalability
- Eventual consistency

---

# Characteristics Table

| Property | Saga |
|-----------|-------|
| Global Lock | No |
| Global Transaction | No |
| Compensation | Yes |
| Eventual Consistency | Yes |
| Independent Services | Yes |

---

# Advantages

- Cloud native
- Microservice friendly
- No distributed locking
- Better scalability
- Independent deployment

---

# Challenges

- Compensation logic
- Event ordering
- Duplicate events
- Retry handling
- Monitoring

---

# Compensation Is Not Rollback

Very important distinction.

Rollback:

Database undoes work.

Compensation:

Business performs opposite action.

Example:

Rollback:

```
DELETE INSERTED ROW
```

Compensation:

```
Issue Refund
```

Original payment remains in audit logs.

Business reverses the outcome.

---

# Examples of Compensation

| Action | Compensation |
|----------|--------------|
| Charge Card | Refund Card |
| Reserve Inventory | Release Inventory |
| Create Booking | Cancel Booking |
| Send Coupon | Revoke Coupon |
| Award Loyalty Points | Deduct Points |

---

# Golden Rule

Every forward action should have:

A defined compensation.

Otherwise,

the workflow cannot recover gracefully.

---

# 18. Choreography vs Orchestration

There are two primary ways to implement Saga.

```text
Choreography

or

Orchestration
```

---

# Choreography

Services communicate using events.

No central coordinator.

---

# Example

```text
Order Created

↓

Payment Service

↓

Payment Completed

↓

Inventory Service

↓

Inventory Reserved

↓

Shipping Service

↓

Shipment Created
```

Each service reacts independently.

---

# Advantages

- Loose coupling
- Highly scalable
- Independent deployment

---

# Disadvantages

- Difficult debugging
- Event explosion
- Hidden workflow
- Harder monitoring

---

# Orchestration

A central component controls the workflow.

```text
Saga Orchestrator

↓

Order

↓

Payment

↓

Inventory

↓

Shipping
```

Coordinator sends commands.

Participants respond.

---

# Advantages

- Easier monitoring
- Clear workflow
- Simpler debugging

---

# Disadvantages

- Central dependency
- Additional infrastructure

---

# Comparison

| Choreography | Orchestration |
|---------------|---------------|
| Event Driven | Command Driven |
| No Central Controller | Central Orchestrator |
| Highly Decoupled | Easier Visibility |
| Harder Debugging | Simpler Monitoring |

---

# Which Should You Choose?

Small systems:

Choreography often works well.

Large enterprise workflows:

Orchestration usually provides better governance, visibility, retries, and recovery.

---

# Architecture Evolution

```text
Monolith

↓

2PC

↓

SOA

↓

Microservices

↓

Saga

↓

Event-Driven

↓

Workflow Engines

↓

Autonomous Business Processes
```

---

# Architect's Perspective

The biggest mindset change is this:

Traditional databases guarantee:

> **Technical Atomicity**

Modern distributed systems guarantee:

> **Business Correctness**

These are not the same.

A Saga may temporarily leave the system in an intermediate state,

but eventually drives it toward the correct business outcome through compensation.

This philosophy underpins modern platforms such as:

- E-commerce
- Ride Sharing
- Food Delivery
- Banking
- Insurance
- Travel Booking
- Cloud-native SaaS

---

**End of Part 4**

The next section will cover:

- **Try-Confirm-Cancel (TCC)**
- **Outbox Pattern**
- **Inbox Pattern**
- **Transactional Messaging**
- **Event Sourcing**
- **Idempotency**
- **Exactly Once vs At Least Once vs At Most Once**
- **Modern Distributed Transaction Patterns**
