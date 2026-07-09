
# 9. Business Impact of Distributed Transactions

> **Distributed transactions exist to protect business correctness—not technical elegance.**

Customers do not care whether your architecture uses:

- Saga
- Kafka
- Two-Phase Commit
- Outbox Pattern

They care about outcomes.

Questions customers ask:

- Was I charged correctly?
- Was my order created?
- Will my package arrive?
- Why did I receive two confirmation emails?
- Why was money deducted twice?

These are distributed transaction problems.

---

# Business Perspective

Modern enterprises are composed of many independent services.

Example:

```text
Customer

↓

API Gateway

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

Analytics
```

Every service owns:

- Its own database
- Its own deployment
- Its own failures

Business processes span all of them.

---

# Revenue Impact

Suppose:

Customer purchases:

```
MacBook Pro

₹250,000
```

Payment succeeds.

Inventory reservation fails.

Warehouse never ships.

Customer waits.

Eventually requests refund.

Business experiences:

- Lost sale
- Refund processing
- Customer support cost
- Reputation damage

The technical failure becomes a business problem.

---

# Banking Example

Money transfer:

```text
Account A

↓

Debit

↓

Credit

↓

Account B
```

Debit succeeds.

Credit fails.

Money appears to disappear.

Even if eventually corrected,

customer confidence is damaged.

---

# Airline Booking

Booking requires:

- Seat Reservation
- Payment
- Loyalty Points
- Ticket Generation
- Email

Suppose:

Seat reserved.

Payment succeeds.

Ticket generation fails.

Customer has:

No ticket.

Airline has:

Received payment.

---

# Healthcare

Patient schedules surgery.

Workflow:

```text
Appointment

↓

Insurance Approval

↓

Operating Room

↓

Doctor

↓

Medical Record
```

If one system fails,

patient care may be delayed.

Distributed transactions become life-critical.

---

# Financial Trading

Trade execution involves:

- Order Matching
- Risk Validation
- Position Update
- Portfolio Update
- Compliance
- Settlement

Failure in one step affects legal compliance.

---

# Customer Trust

Customers remember:

Incorrect outcomes.

Not architectural complexity.

Examples:

- Duplicate payment
- Missing order
- Wrong invoice
- Lost booking

Trust decreases rapidly.

---

# Regulatory Impact

Industries requiring strict correctness:

- Banking
- Insurance
- Healthcare
- Government
- Stock Exchanges

Distributed transaction failures may violate:

- Financial regulations
- Audit requirements
- Legal obligations

---

# Cost of Failure

| Failure | Business Impact |
|-----------|----------------|
| Duplicate Payment | Refunds + Reputation |
| Lost Order | Revenue Loss |
| Inventory Mismatch | Operational Cost |
| Missing Shipment | Customer Churn |
| Duplicate Shipment | Logistics Cost |
| Incorrect Invoice | Financial Audit Issues |

---

# 10. Transaction Lifecycle

Every distributed transaction follows a lifecycle.

```text
Request

↓

Validation

↓

Business Decision

↓

Execution

↓

Coordination

↓

Completion

↓

Recovery

↓

Audit
```

Unlike local transactions,

distributed transactions continue long after the initial request completes.

---

# Example

Customer places an order.

```text
Order Created

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
```

Each step:

- May succeed
- May fail
- May retry
- May compensate

---

# Transaction States

A distributed transaction passes through multiple states.

```text
Created

↓

Running

↓

Waiting

↓

Completed

or

Compensating

↓

Finished
```

Some workflows may remain active for:

- Minutes
- Hours
- Days

---

# Example Timeline

```
10:00

Order Created

↓

10:01

Payment Success

↓

10:03

Inventory Reserved

↓

10:07

Warehouse Picks Item

↓

10:20

Shipment Created

↓

Next Day

Delivered
```

The business transaction lasts much longer than any individual database transaction.

---

# Long-Running Transactions

Many business workflows cannot complete immediately.

Examples:

- Mortgage approval
- Insurance claims
- Loan processing
- International shipping
- Hotel reservations

These are:

Long-running business transactions.

---

# Characteristics

They involve:

- Human approval
- External systems
- Time delays
- Multiple retries
- Compensation

Traditional ACID transactions are unsuitable.

---

# 11. Transaction Boundaries

One of the most important architectural decisions.

Question:

> **Where should a transaction begin and end?**

---

# Incorrect Boundary

```text
Gateway

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

One Giant Transaction
```

Problems:

- Long locks
- Poor scalability
- Cascading failures
- Difficult recovery

---

# Correct Boundary

Each service owns:

Its own transaction.

Example:

```text
Order Service

Local Transaction

↓

Publish Event

↓

Payment Service

Local Transaction

↓

Publish Event

↓

Inventory Service
```

Independent.

Loosely coupled.

---

# Why Small Transactions Win

Advantages:

- Short locks
- Better scalability
- Independent recovery
- Easier deployment

---

# Transaction Ownership

Golden Rule:

> **Each service owns exactly one database and one local transaction.**

Never allow:

Service A

↓

Direct transaction inside

Service B's database.

---

# 12. Transaction Coordinator

Some distributed transaction strategies use a coordinator.

The coordinator:

- Tracks progress
- Collects responses
- Decides commit
- Decides rollback

---

# Coordinator Architecture

```text
Coordinator

↓

Order

↓

Payment

↓

Inventory

↓

Shipping
```

Coordinator knows:

Current transaction state.

---

# Responsibilities

- Start transaction
- Track participants
- Handle timeouts
- Retry
- Rollback
- Recovery

---

# Advantages

Centralized control.

Simpler orchestration.

---

# Disadvantages

- Single logical coordinator
- Operational complexity
- Potential bottleneck
- Additional infrastructure

---

# Coordinator vs Choreography

Coordinator:

```text
One Brain

↓

Controls Everyone
```

---

Choreography:

```text
Events

↓

Services React

↓

Next Event
```

No central controller.

Both approaches will be explored later in this chapter.

---

# 13. Failure Scenarios

Distributed systems assume failures are normal.

Architectures must be designed accordingly.

---

# Scenario 1

Payment succeeds.

Inventory fails.

```text
Order ✓

Payment ✓

Inventory ✗

Shipping ?

Notification ?
```

Question:

Should payment be refunded?

---

# Scenario 2

Inventory succeeds.

Shipping timeout.

Did shipping:

- Fail?
- Succeed?
- Retry?

System cannot immediately know.

---

# Scenario 3

Payment request times out.

Customer retries.

Original payment actually succeeded.

Without idempotency:

```
Duplicate Payment
```

---

# Scenario 4

Order event published.

Message broker crashes.

Did consumers receive it?

Unknown.

---

# Scenario 5

Notification sent.

Order later cancelled.

Customer receives conflicting messages.

---

# Failure Categories

| Failure | Example |
|-----------|---------|
| Service Failure | Payment unavailable |
| Database Failure | Commit fails |
| Network Failure | Timeout |
| Messaging Failure | Lost event |
| Retry Failure | Duplicate processing |
| Human Failure | Manual cancellation |

---

# 14. Why ACID Doesn't Scale Across Microservices

Traditional databases solve:

```sql
BEGIN

...

COMMIT
```

within one database.

Microservices intentionally avoid:

Shared databases.

---

# Single Database

```text
Application

↓

Database
```

Simple ACID.

---

# Microservices

```text
Order DB

Payment DB

Inventory DB

Shipping DB
```

Independent ownership.

Independent deployment.

---

# Why Global ACID Fails

Imagine:

```text
BEGIN

Order DB

↓

Payment DB

↓

Inventory DB

↓

Shipping DB

↓

COMMIT
```

Problems:

- Long-running locks
- Slow services
- Network failures
- Coordinator failures
- Poor scalability

One slow participant blocks everyone.

---

# Independent Deployment

Microservices evolve independently.

Example:

Payment Service:

Version 12

Inventory Service:

Version 7

Shipping Service:

Version 19

Global transactions tightly couple independent services.

---

# Availability Impact

If:

Notification Service

is down,

should:

Payment Service

stop accepting payments?

Usually:

No.

Business prefers eventual completion.

---

# The Shift in Thinking

Traditional systems ask:

> "How do we rollback?"

Modern distributed systems ask:

> "How do we compensate?"

This is the key architectural mindset.

---

# Decision Framework

Before selecting a distributed transaction strategy, ask:

1. Can the business tolerate eventual consistency?
2. Is rollback technically possible?
3. Can failed work be compensated?
4. Are operations idempotent?
5. Are services independently deployable?
6. What is the maximum acceptable recovery time?
7. What happens if the same request executes twice?
8. What happens if one service is permanently unavailable?

The answers guide the architecture—not the technology.

---

# Architecture Evolution Timeline

```text
Single Database

↓

Multiple Tables

↓

Multiple Schemas

↓

Multiple Databases

↓

Service-Oriented Architecture

↓

Microservices

↓

Event-Driven Architecture

↓

Saga-Based Business Transactions

↓

Autonomous Distributed Systems
```

---

# Architect's Perspective

Experienced architects no longer think in terms of:

> **Database Transactions**

Instead, they think in terms of:

> **Business Workflows**

The goal is no longer:

"Rollback everything."

The goal is:

"Maintain business correctness despite partial failures."

That philosophy underpins every modern distributed transaction pattern.

---

**End of Part 2**

The next section will cover:

- **Two-Phase Commit (2PC)**
- **Three-Phase Commit (3PC)**
- **XA Transactions**
- **Prepare Phase**
- **Commit Phase**
- **Coordinator Failure**
- **Blocking Problems**
- **Performance Trade-offs**
- **When (and When Not) to Use 2PC**
