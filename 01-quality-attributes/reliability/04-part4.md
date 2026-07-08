
# 13. Architecture Decisions

> **Architecture determines whether a system merely works under ideal conditions or continues producing correct business outcomes under real-world conditions.**

Every architecture represents a collection of decisions.

Some decisions improve scalability.

Some improve availability.

Others improve security.

This chapter focuses on decisions whose primary objective is **ensuring correctness**.

Before discussing technologies, architects first decide **how correctness should be protected**.

---

# Reliability Decision Framework

Every architectural decision should answer the following questions:

```text
Business Rule

↓

What Can Go Wrong?

↓

Failure Analysis

↓

Protect Business Data

↓

Choose Architecture Pattern

↓

Evaluate Trade-offs

↓

Implement Technology

↓

Measure Reliability
```

Notice that **technology appears near the end**.

Reliability begins with business rules.

---

# Reliability Design Principles

Before selecting architectural patterns, experienced architects generally follow several guiding principles.

---

## Principle 1 — Business Data Is More Valuable Than Infrastructure

Infrastructure can always be recreated.

Business data often cannot.

Consider these failures:

- Lost payment
- Corrupted medical history
- Incorrect tax filing
- Missing audit log
- Deleted legal contract

Replacing servers is easy.

Reconstructing business truth is often impossible.

Therefore:

> Protect data before protecting infrastructure.

---

## Principle 2 — Every Business Operation Must Be Traceable

If an architect cannot answer:

> "What happened?"

The system is not sufficiently reliable.

Every important business event should be traceable.

Examples:

- Payment Created
- Order Confirmed
- Invoice Generated
- Shipment Delivered

Reliable systems maintain an auditable history.

---

## Principle 3 — Duplicate Requests Are Normal

Distributed systems naturally generate duplicate requests because of:

- Network retries
- Browser refresh
- Mobile reconnection
- Queue redelivery
- Consumer restart

Architects assume duplicates **will occur**.

Applications must safely handle them.

---

## Principle 4 — Never Assume Perfect Communication

Network communication is unreliable.

Messages may:

- Arrive late
- Arrive twice
- Never arrive
- Arrive out of order

Architectural decisions should account for these realities.

---

## Principle 5 — Prefer Recovery Over Perfection

Perfect systems do not exist.

Reliable systems:

- Detect inconsistencies.
- Repair data.
- Replay events.
- Recover gracefully.

---

# 13.1 ACID Transactions

One of the earliest architectural decisions concerns transactional boundaries.

---

## Why ACID Exists

Imagine transferring money.

```
Withdraw ₹100

↓

Deposit ₹100
```

If withdrawal succeeds but deposit fails:

Money disappears.

The business rule is violated.

---

## ACID Guarantees

| Property | Purpose |
|-----------|----------|
| Atomicity | Entire operation succeeds or fails. |
| Consistency | Business rules remain valid. |
| Isolation | Concurrent operations do not interfere. |
| Durability | Committed data survives failures. |

---

## Business Benefits

- Financial correctness
- Inventory consistency
- Data integrity
- Predictable recovery

---

## When NOT to Use Large Transactions

Long-running distributed transactions reduce:

- Scalability
- Availability
- Performance

Architects often replace them with alternative patterns.

---

# 13.2 Idempotency

One of the most important architectural concepts in distributed systems.

---

## Why Idempotency Exists

Suppose a client retries a payment request.

```
POST /payments

↓

Network Timeout

↓

Retry

↓

Retry Again
```

Without idempotency:

Customer may be charged three times.

---

## Business Goal

Regardless of how many times a request is received:

Business outcome remains identical.

---

## Example

```
Payment Request

Idempotency-Key:

ABC123
```

Repeated requests with the same key return the original result.

No duplicate payment occurs.

---

## Business Benefits

- Safe retries
- Better user experience
- Reduced reconciliation
- Higher confidence

---

# 13.3 Event Sourcing

Traditional applications store current state.

```
Balance = ₹12,500
```

Event Sourcing stores every change.

```
Account Opened

↓

Deposit ₹10,000

↓

Deposit ₹5,000

↓

Withdraw ₹2,500
```

Current balance is derived from history.

---

## Why Architects Choose Event Sourcing

Benefits include:

- Complete audit trail
- Easy debugging
- Replay capability
- Historical reconstruction
- Compliance support

---

## Trade-offs

Advantages

- Perfect history
- Easier auditing
- Better recovery

Disadvantages

- Increased complexity
- Event versioning
- Storage growth

---

# 13.4 CQRS

Reliability often requires separating reads from writes.

Instead of:

```
Users

↓

Single Database
```

Architects separate responsibilities.

```
Commands

↓

Write Model

↓

Events

↓

Read Model

↓

Queries
```

---

## Why?

Write operations focus on:

- Validation
- Transactions
- Business rules

Read operations focus on:

- Performance
- Reporting
- Search

Each side evolves independently.

---

# 13.5 Saga Pattern

Distributed transactions rarely scale well.

Instead of:

```
Order

↓

Payment

↓

Inventory

↓

Shipping
```

Each step becomes an independent transaction.

If Shipping fails:

Compensating actions execute.

```
Cancel Inventory

↓

Refund Payment
```

---

## Business Benefits

- Reliability
- Scalability
- Recovery
- Loose coupling

---

# 13.6 Outbox Pattern

One of the most important patterns for reliable messaging.

---

## Problem

Application:

```
Save Order

↓

Publish Event
```

Database succeeds.

Message publication fails.

Result:

Order exists.

Inventory never updates.

---

## Solution

```
Database Transaction

↓

Save Order

+

Save Outbox Event

↓

Commit
```

A background publisher reliably delivers messages.

---

## Benefits

- No lost events
- Reliable publishing
- Easier recovery

---

# 13.7 Inbox Pattern

The Outbox solves producer reliability.

Inbox solves consumer reliability.

---

Consumer stores:

```
Message ID

↓

Already Processed?

↓

Yes → Ignore

No → Process
```

---

Benefits:

- Duplicate prevention
- Idempotent consumers
- Reliable retries

---

# 13.8 Immutable Logs

Instead of modifying history:

Append new events.

```
Created

↓

Updated

↓

Approved

↓

Cancelled
```

History remains complete.

---

## Business Benefits

- Auditing
- Compliance
- Recovery
- Investigation
- Historical reporting

---

# 13.9 Consensus Protocols

Distributed systems require agreement.

Questions include:

- Who is leader?
- Which write is correct?
- Which node owns the truth?

Consensus protocols solve these problems.

Examples:

- Raft
- Paxos

---

## Business Benefits

- Prevent Split Brain
- Protect consistency
- Reliable failover

---

# 13.10 Validation

Architects should never assume incoming data is correct.

Validation occurs at multiple layers.

Examples:

- API validation
- Domain validation
- Database constraints
- Business rule validation

---

Example:

```
Transfer Amount

Must Be

>

0
```

Simple validation prevents many production incidents.

---

# 13.11 Audit Trail

Every important business operation should answer:

- Who?
- What?
- When?
- Where?
- Why?

Example:

```
User:

John

Action:

Updated Customer Address

Time:

10:42 UTC
```

Audit trails support:

- Compliance
- Debugging
- Investigations
- Security

---

# 13.12 Versioning

Concurrent updates require version tracking.

Example:

```
Customer

Version 12
```

If another update already produced:

```
Version 13
```

Version conflict occurs.

Application avoids overwriting newer data.

---

# 13.13 Checksums

Checksums detect corruption.

Example:

```
File

↓

SHA-256

↓

Compare

↓

Valid?
```

Widely used in:

- File transfer
- Storage
- Backup
- Replication

---

# 13.14 Retry Strategy

Retries remain valuable.

However:

Reliable retries require:

- Idempotency
- Backoff
- Jitter
- Retry limits

Retries without correctness produce duplicate business operations.

---

# 13.15 Dead Letter Queue (DLQ)

Some messages cannot be processed.

Repeated retries waste resources.

Instead:

```
Message

↓

Retry

↓

Retry

↓

Retry

↓

Dead Letter Queue
```

Benefits:

- Preserve failed messages
- Manual investigation
- Safe recovery

---

# 13.16 Reconciliation

Reliable businesses verify correctness continuously.

Examples:

Banking

```
Payments

=

Ledger
```

Inventory

```
Orders

=

Stock Movement
```

If differences appear:

Generate alerts.

---

# 13.17 Graceful Degradation

Not every operation requires identical correctness guarantees.

Example:

```
Recommendation

Unavailable

↓

Checkout

Still Works
```

Protect core business functions first.

---

# 13.18 Exactly-Once Processing

Many businesses request:

> Exactly Once Delivery.

Architecturally,

Exactly-once **delivery** is extremely difficult across distributed systems.

Instead,

Architects design:

- At-least-once delivery
- Idempotent processing

Result:

Business outcome behaves as though processed exactly once.

---

# 13.19 Reliable State Machines

Many business processes naturally form state machines.

Example:

```
Order

Created

↓

Confirmed

↓

Packed

↓

Shipped

↓

Delivered
```

Reliable systems validate legal transitions.

Prevent invalid transitions such as:

```
Delivered

↓

Packed
```

---

# Architecture Decision Summary

| Decision | Business Problem Solved | Why It Improves Reliability |
|-----------|-------------------------|-----------------------------|
| ACID Transactions | Partial updates | Preserves consistency |
| Idempotency | Duplicate requests | Prevents repeated business operations |
| Event Sourcing | Missing history | Complete auditability |
| CQRS | Mixed workloads | Independent optimization |
| Saga | Distributed workflows | Reliable compensation |
| Outbox | Lost events | Guaranteed publication |
| Inbox | Duplicate events | Safe processing |
| Immutable Logs | Lost history | Traceability |
| Consensus | Split Brain | Single source of truth |
| Validation | Invalid input | Protects business rules |
| Audit Trail | Missing accountability | Investigation & compliance |
| Versioning | Concurrent updates | Prevents lost updates |
| Checksums | Data corruption | Detects integrity issues |
| Dead Letter Queue | Poison messages | Safe failure handling |
| Reconciliation | Silent inconsistencies | Continuous correctness verification |

---

## Key Architectural Lesson

Reliable systems are not created by adding more servers.

They are created by making deliberate architectural decisions that protect **business correctness**.

An experienced architect asks questions such as:

- What happens if this request is processed twice?
- What happens if this message is lost?
- What happens if two users update the same record simultaneously?
- What happens if only half of the workflow succeeds?
- Can we reconstruct the complete business history after an incident?

These questions guide the architecture toward systems that customers and businesses can trust.

---

**End of Part 4**

The next part will cover **Section 14: Mechanisms**, explaining how technologies such as relational databases, Kafka, RabbitMQ, Redis, Spring Boot, Kubernetes, AWS, Azure, and GCP implement these reliability decisions, followed by the **Decision Matrix** for different business scenarios.
