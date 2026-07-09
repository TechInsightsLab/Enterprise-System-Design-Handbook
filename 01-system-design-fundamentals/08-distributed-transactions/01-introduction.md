
# Distributed Transactions

> **Part I – System Design Foundations**

---

# Distributed Transactions

| Attribute | Value |
|-----------|--------|
| **Version** | 2.0 |
| **Part** | Part I – System Design Foundations |
| **Chapter** | 8 |
| **Reading Time** | ~260–320 Minutes |
| **Difficulty** | Advanced |
| **Last Updated** | July 2026 |

---

> [!NOTE]
> **A distributed transaction is a business transaction that spans multiple services, databases, message brokers, or external systems while attempting to maintain business correctness despite failures.**

Distributed transactions are one of the **hardest problems in software architecture**.

Unlike a local database transaction, a distributed transaction must coordinate multiple independent systems that:

- Fail independently
- Scale independently
- Deploy independently
- Have different latency
- Have different consistency guarantees

There is **no perfect distributed transaction mechanism**.

Every solution involves trade-offs.

---

# Table of Contents

## Part 1 – Foundations

- Learning Objectives
- Prerequisites
- Business Story
- Terminology
- Why Distributed Transactions Matter
- Local vs Distributed Transactions
- ACID Revisited
- The Distributed Transaction Problem

## Part 2 – Core Concepts

- Transaction Boundaries
- Atomicity Across Services
- Business Transactions
- Technical Transactions
- Transaction Lifecycle
- Failure Scenarios
- Why Distributed Transactions Are Hard

## Part 3 – Distributed Transaction Patterns

- Two-Phase Commit (2PC)
- Three-Phase Commit (3PC)
- Saga Pattern
- Choreography
- Orchestration
- TCC (Try-Confirm-Cancel)
- Outbox Pattern
- Inbox Pattern
- Transactional Messaging
- Event Sourcing
- Compensation Transactions

## Part 4 – Architecture

- Distributed Locks
- Idempotency
- Retry Strategies
- Exactly Once
- At Least Once
- At Most Once
- Ordering
- Deduplication
- Failure Recovery
- Decision Matrix

## Part 5 – Implementation

- Spring Boot
- Kafka
- RabbitMQ
- PostgreSQL
- MySQL
- Redis
- Kubernetes
- AWS
- Azure
- GCP

## Part 6 – Production

- Monitoring
- Metrics
- Alerting
- Production Incidents
- Anti-patterns
- Chaos Testing

## Part 7 – Enterprise

- Amazon
- Uber
- Netflix
- Stripe
- PayPal
- Google
- Airbnb

## Part 8 – Architect Toolkit

- Architecture Review Checklist
- Production Readiness Checklist
- ADR
- Decision Tree
- Cost Analysis
- Cloud Mapping
- Technology Comparison
- Common Myths

## Part 9 – Interview Preparation

- Beginner Questions
- Intermediate Questions
- Senior Architect Questions
- Staff Questions
- Principal Questions
- Corner Cases

## Part 10 – Revision

- Summary
- Cheat Sheet
- Research Papers
- Further Reading
- GitHub Structure

---

# Learning Objectives

After completing this chapter, you should be able to:

- Explain why distributed transactions are fundamentally difficult.
- Distinguish technical transactions from business transactions.
- Explain why ACID does not scale across microservices.
- Compare 2PC, 3PC, Saga, and TCC.
- Design reliable event-driven business workflows.
- Prevent duplicate processing.
- Design idempotent services.
- Choose the correct transaction strategy for enterprise systems.
- Answer advanced distributed transaction interview questions.

---

# Prerequisites

Readers should already understand:

- High Availability
- Reliability
- Scalability
- Performance
- Fault Tolerance
- Resilience
- Consistency

Knowledge of:

- Databases
- Messaging
- Kafka
- Microservices

will be helpful.

---

# Previous Chapters

- High Availability
- Reliability
- Scalability
- Performance
- Fault Tolerance
- Resilience
- Consistency

---

# Next Chapters

- Concurrency
- Data Management
- Event-Driven Architecture

---

# Terminology

| Term | Definition |
|------|------------|
| Transaction | A unit of work that must satisfy defined correctness guarantees |
| Local Transaction | Transaction within a single database |
| Distributed Transaction | Transaction spanning multiple systems |
| Coordinator | Component coordinating transaction execution |
| Participant | Service participating in a transaction |
| Commit | Permanently apply changes |
| Rollback | Undo changes |
| Compensation | Business action that reverses previous work |
| Idempotency | Safe repeated execution producing the same result |
| Saga | Sequence of local transactions connected through events |

---

# Opening Business Story

Imagine placing an order on a large e-commerce platform.

The following services participate:

```text
Order Service

↓

Payment Service

↓

Inventory Service

↓

Shipping Service

↓

Notification Service
```

Customer clicks:

```
Place Order
```

Everything appears successful.

Then:

Payment succeeds.

Inventory reservation fails.

Shipping request already sent.

Email confirmation already delivered.

Customer has been charged.

Warehouse never ships.

Support receives complaints.

Refund process begins.

Business loses money.

---

Question:

Did the transaction succeed?

Or fail?

The answer is:

**Both.**

Some services succeeded.

Others failed.

This is the distributed transaction problem.

---

# Another Example

Ride-sharing platform.

Booking requires:

```text
Driver Matching

↓

Payment Authorization

↓

Ride Creation

↓

Driver Notification

↓

Customer Notification
```

Suppose:

Payment succeeds.

Driver declines.

Customer receives:

```
Ride Confirmed
```

Driver receives nothing.

Should payment be refunded?

Should another driver be assigned?

Should the ride remain active?

Business rules—not databases—must answer these questions.

---

# Banking Example

Transfer:

```
₹10,000

Account A

↓

Account B
```

Traditional database:

```sql
BEGIN

Debit A

Credit B

COMMIT
```

Easy.

Now imagine:

Account A exists in:

```
Bank A
```

Account B exists in:

```
Bank B
```

Different databases.

Different systems.

Different organizations.

No shared transaction.

How do both banks guarantee correctness?

Distributed transactions exist to solve this problem.

---

# Introduction

Traditional databases provide:

```text
BEGIN

...

COMMIT
```

Everything succeeds.

Or everything rolls back.

Microservices change this model.

Each service owns:

- Its own database
- Its own transactions
- Its own deployment
- Its own lifecycle

No service should directly control another service's database.

As a result,

one global transaction becomes extremely difficult.

---

# Definition

A distributed transaction is:

> **A business operation that spans multiple independent systems while attempting to preserve business correctness despite partial failures, network partitions, retries, and asynchronous communication.**

Notice:

It says:

**Business correctness**

Not necessarily:

```
One Database Transaction
```

This distinction is fundamental.

---

# Why Distributed Transactions Matter

Modern enterprise systems consist of:

```text
Orders

Payments

Inventory

Shipping

Billing

Notifications

Analytics
```

Each service owns its own database.

Business workflows span multiple services.

Examples:

- Place Order
- Book Flight
- Reserve Hotel
- Transfer Money
- Create Insurance Policy
- Schedule Appointment

None fit inside a single local transaction.

---

# Local Transaction

Traditional architecture:

```text
Application

↓

Database
```

One database.

One transaction.

```sql
BEGIN

Update Orders

Update Inventory

Update Payment

COMMIT
```

Simple.

Reliable.

Fast.

---

# Distributed Transaction

Microservices:

```text
Order Service

↓

Order DB

-------------------

Payment Service

↓

Payment DB

-------------------

Inventory Service

↓

Inventory DB
```

Each database commits independently.

No single ACID transaction spans them all.

---

# The Core Problem

Suppose:

Order Service commits.

```text
Order Created
```

Payment Service fails.

Inventory already reserved.

Notification already sent.

Now answer:

Should:

- Cancel order?
- Refund payment?
- Release inventory?
- Send correction email?

Traditional rollback is impossible.

Business compensation becomes necessary.

---

# ACID Revisited

Earlier in our database discussions,

ACID seemed straightforward.

```text
Atomicity

Consistency

Isolation

Durability
```

Within one database,

these guarantees work extremely well.

Across multiple services,

each property becomes significantly harder.

---

# Atomicity

Local transaction:

```
Everything

or

Nothing
```

Distributed transaction:

Some services already committed.

Rollback may be impossible.

---

# Consistency

Database consistency:

Simple.

Business consistency:

Far more difficult.

---

# Isolation

Two microservices execute independently.

No global lock exists.

---

# Durability

Each service persists data independently.

Recovery becomes decentralized.

---

# Business Transactions vs Technical Transactions

One of the biggest mindset shifts for architects.

---

# Technical Transaction

Example:

```sql
BEGIN

INSERT

UPDATE

DELETE

COMMIT
```

Scope:

Single database.

---

# Business Transaction

Example:

```
Place Order

↓

Charge Customer

↓

Reserve Inventory

↓

Create Shipment

↓

Notify Customer
```

Multiple services.

Multiple databases.

Multiple technologies.

---

# Business Transaction Timeline

```text
Customer

↓

Order

↓

Payment

↓

Inventory

↓

Shipping

↓

Notification

↓

Invoice

↓

Analytics
```

Every step has independent failure modes.

---

# Why Distributed Transactions Are Hard

Distributed systems introduce uncertainty.

Failures include:

- Network partitions
- Message duplication
- Timeouts
- Partial commits
- Service crashes
- Database failures
- Retry storms
- Duplicate requests

Every failure creates uncertainty.

---

# Failure Example

```text
Order

↓

Payment

✓

↓

Inventory

✗

↓

Shipping

?

↓

Notification

✓
```

Questions:

- Did shipping execute?
- Should payment be refunded?
- Should inventory retry?
- Should notification be revoked?

Distributed transactions require recovery strategies.

---

# The Fundamental Problem

Distributed transactions answer one difficult question:

> **How can independent systems collectively perform one business operation without violating business correctness when failures occur at any step?**

There is no universal solution.

Different architectures solve this differently.

---

# Architect's Perspective

Junior engineers often ask:

> "How do I make one transaction across all databases?"

Experienced architects ask:

- Can the business tolerate eventual consistency?
- Can failures be compensated instead of rolled back?
- Which operations require immediate correctness?
- Which operations can be asynchronous?
- What happens if retries occur?
- How do we prevent duplicate business operations?

Those questions define modern distributed transaction architecture.

---

**End of Part 1**

The next part will cover:

- **Business Impact**
- **Transaction Lifecycle**
- **Failure Scenarios**
- **Technical vs Business Consistency**
- **Why ACID Doesn't Scale Across Microservices**
- **Transaction Boundaries**
- **Transaction Coordinator**
- **Distributed Transaction Decision Framework**
