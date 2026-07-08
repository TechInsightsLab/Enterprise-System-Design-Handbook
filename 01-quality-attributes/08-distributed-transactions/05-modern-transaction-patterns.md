
# 19. Try-Confirm-Cancel (TCC)

> **TCC (Try-Confirm-Cancel) is a distributed transaction pattern that explicitly separates resource reservation from final commitment.**

Unlike Saga,

where compensation happens **after** a completed local transaction,

TCC reserves resources first.

Only after every participant is ready does the transaction become permanent.

---

# Why TCC Exists

Imagine booking:

- Flight
- Hotel
- Rental Car

If payment fails,

you don't want to permanently reserve:

- Flight seat
- Hotel room
- Rental car

Instead,

temporarily reserve everything.

Only when all participants agree,

confirm the reservations.

---

# Three Phases

As the name suggests:

```text
Try

↓

Confirm

↓

Cancel
```

---

# Phase 1 — Try

Reserve resources.

Do NOT permanently commit.

Example:

Payment

```
Reserve ₹20,000 Credit
```

Inventory

```
Reserve 1 Laptop
```

Shipping

```
Reserve Delivery Slot
```

Everything remains provisional.

---

# Phase 2 — Confirm

All participants succeeded.

Coordinator sends:

```
CONFIRM
```

Participants permanently commit.

---

# Phase 3 — Cancel

Suppose:

Inventory unavailable.

Coordinator sends:

```
CANCEL
```

Participants release reservations.

---

# TCC Workflow

```text
Customer

↓

Try Payment

↓

Try Inventory

↓

Try Shipping

↓

Everything Ready?

↓

YES

↓

Confirm All

--------------------

NO

↓

Cancel All
```

---

# Example

Customer purchases:

Laptop.

---

Try

```text
Payment

↓

Reserve Money

Inventory

↓

Reserve Laptop

Shipping

↓

Reserve Delivery
```

---

Confirm

```text
Capture Payment

↓

Reduce Inventory

↓

Create Shipment
```

---

Cancel

```text
Release Payment Hold

↓

Release Inventory

↓

Cancel Shipment Reservation
```

---

# Characteristics

| Property | TCC |
|----------|------|
| Reservation | Yes |
| Compensation | Explicit Cancel |
| Global Lock | No |
| Eventual Consistency | Yes |
| Business Awareness | Required |

---

# Advantages

- Better business control
- Clear workflow
- Resource reservation
- Suitable for financial systems

---

# Disadvantages

- Complex implementation
- Every service needs Try/Confirm/Cancel APIs
- More network calls
- Longer business workflow

---

# Suitable Use Cases

- Airline booking
- Hotel reservation
- Payment authorization
- Ticket booking
- Financial trading

---

# Saga vs TCC

| Feature | Saga | TCC |
|----------|------|------|
| Local Commit | Immediately | After Confirm |
| Compensation | Business Action | Explicit Cancel |
| Resource Reservation | No | Yes |
| Complexity | Medium | High |
| Business Control | Medium | High |

---

# 20. Outbox Pattern

> **The Outbox Pattern guarantees that database changes and published events remain consistent without requiring distributed transactions.**

One of the biggest problems in event-driven systems:

```text
Save Order

↓

Publish Event
```

What happens if:

Database commit succeeds,

but event publication fails?

Other services never know the order exists.

---

# Example

```text
Order Saved

✓

↓

Kafka Publish

✗
```

Result:

Order exists.

Inventory never reserves stock.

---

# Outbox Solution

Instead of publishing immediately,

save the event inside the same database transaction.

```text
BEGIN

Insert Order

Insert Outbox Event

COMMIT
```

Now both succeed together.

---

# Architecture

```text
Application

↓

Database

├── Orders

└── Outbox
```

Background process:

```text
Outbox

↓

Kafka

↓

Consumers
```

---

# Workflow

```text
Create Order

↓

Insert Order

↓

Insert Outbox Event

↓

Commit

↓

Background Publisher

↓

Kafka

↓

Consumers
```

---

# Advantages

- No lost events
- Reliable publishing
- No XA transaction
- Simple recovery

---

# Recovery

Suppose Kafka unavailable.

Database already contains:

```
Pending Event
```

Publisher retries later.

Eventually:

Event delivered.

---

# Outbox Table Example

| Event ID | Aggregate | Status |
|-----------|-----------|--------|
| 1001 | OrderCreated | Pending |
| 1002 | PaymentCompleted | Published |

---

# Polling Publisher

Common implementation.

```text
Scheduler

↓

Query Outbox

↓

Publish Events

↓

Mark Published
```

---

# CDC (Change Data Capture)

Alternative approach.

Instead of polling,

capture database changes.

Popular tool:

```
Debezium
```

Workflow:

```text
Database

↓

Transaction Log

↓

Debezium

↓

Kafka
```

No polling required.

---

# Advantages of CDC

- Lower latency
- No polling queries
- Better scalability
- Near real-time publishing

---

# 21. Inbox Pattern

If Outbox guarantees reliable publishing,

Inbox guarantees reliable consumption.

---

# Problem

Message broker retries event.

Consumer receives:

```
OrderCreated

↓

OrderCreated
```

Duplicate processing.

---

# Inbox Solution

Consumer stores:

```
Processed Event ID
```

Before processing:

Check Inbox.

---

# Workflow

```text
Receive Event

↓

Inbox Lookup

↓

Already Processed?

↓

YES

Ignore

↓

NO

Process

↓

Save Inbox Record
```

---

# Inbox Table

| Event ID | Status |
|-----------|---------|
| EVT-1001 | Processed |
| EVT-1002 | Processed |

---

# Benefits

- Duplicate prevention
- Safe retries
- Exactly-once processing (application level)

---

# Outbox + Inbox Together

```text
Producer

↓

Outbox

↓

Kafka

↓

Inbox

↓

Consumer
```

Reliable end-to-end messaging.

---

# 22. Transactional Messaging

Distributed systems frequently need:

```
Database Update

+

Message Publish
```

These operations must behave as one logical unit.

---

# Bad Approach

```text
Save Database

↓

Publish Event
```

Failure between steps causes inconsistency.

---

# Better Approach

```text
Database

↓

Outbox

↓

Reliable Publisher

↓

Kafka

↓

Consumers
```

Transactional messaging eliminates lost events.

---

# Common Technologies

- Kafka
- RabbitMQ
- Pulsar
- EventBridge
- Azure Service Bus

---

# 23. Event Sourcing

Traditional systems store:

Current state.

Example:

```
Balance

₹5,000
```

---

Event Sourcing stores:

Every event.

```text
Account Created

↓

Deposit ₹10,000

↓

Withdraw ₹2,000

↓

Withdraw ₹3,000
```

Current balance computed by replaying events.

---

# Advantages

- Complete audit history
- Replay capability
- Easy debugging
- Time travel

---

# Trade-offs

- More storage
- Replay cost
- Event schema evolution
- Higher implementation complexity

---

# CQRS + Event Sourcing

Common combination.

```text
Commands

↓

Events

↓

Event Store

↓

Read Models
```

Writes and reads optimized independently.

---

# 24. Idempotency

> **Idempotency means performing the same operation multiple times produces the same business result.**

One of the most important concepts in distributed transactions.

---

# Why Needed?

Suppose:

Payment request times out.

Client retries.

Without idempotency:

```text
Charge

↓

Charge Again
```

Customer pays twice.

---

# Example

Request:

```http
POST /payments

Idempotency-Key:

abc123
```

Server stores:

```
abc123

↓

Payment Completed
```

Retry:

Same key.

Server returns:

Previous result.

No duplicate payment.

---

# Benefits

- Safe retries
- Duplicate prevention
- Better reliability

---

# Common Uses

- Payments
- Order Creation
- Refunds
- Coupon Redemption
- User Registration

---

# Idempotency Store

```text
Request

↓

Idempotency Table

↓

Already Exists?

↓

YES

Return Previous Response

↓

NO

Execute Business Logic
```

---

# 25. Delivery Guarantees

Messaging systems provide different guarantees.

---

# At Most Once

```text
Send

↓

Maybe Delivered

↓

No Retry
```

Possible:

Lost message.

Never duplicated.

---

# At Least Once

```text
Send

↓

Retry Until Success
```

Guaranteed delivery.

Possible duplicates.

Most common.

---

# Exactly Once

Ideal goal.

```text
Delivered

Exactly

One Time
```

Extremely difficult in distributed systems.

Usually requires:

- Transactions
- Idempotency
- Deduplication

---

# Comparison

| Guarantee | Lost Messages | Duplicates |
|------------|---------------|------------|
| At Most Once | Possible | No |
| At Least Once | No | Possible |
| Exactly Once | No | No (Logical) |

---

# Important Reality

Exactly-once delivery across an entire distributed system is practically unattainable.

What platforms such as Kafka provide is:

> **Exactly-once processing semantics within defined boundaries**, not universal exactly-once delivery.

Application-level idempotency is still essential.

---

# Decision Matrix

| Requirement | Recommended Pattern |
|-------------|---------------------|
| Payment | TCC + Idempotency |
| E-commerce | Saga + Outbox |
| Inventory | Saga + Compensation |
| Event Publishing | Outbox |
| Event Consumption | Inbox |
| Audit Trail | Event Sourcing |
| Retry Safety | Idempotency |
| Reservation | TCC |

---

# Cost Analysis

| Pattern | Complexity | Scalability | Business Correctness |
|----------|------------|-------------|----------------------|
| Saga | Medium | High | High |
| TCC | High | Medium | Very High |
| Outbox | Medium | High | High |
| Inbox | Low | High | High |
| Event Sourcing | High | High | Very High |
| Idempotency | Low | High | Essential |

---

# Architect's Perspective

Modern distributed systems rarely rely on a single transaction pattern.

Instead, successful architectures combine multiple patterns:

- **Saga** for business workflow coordination.
- **Outbox** for reliable event publication.
- **Inbox** for reliable event consumption.
- **Idempotency** for safe retries.
- **TCC** where resource reservation is critical.
- **Event Sourcing** when complete business history is required.

The strength of the architecture lies in **combining these patterns appropriately**, rather than expecting one pattern to solve every distributed transaction problem.

---

**End of Part 5**

The next section will cover:

- **Spring Boot Implementation**
- **Kafka Transactions**
- **RabbitMQ**
- **Redis**
- **Kubernetes**
- **AWS / Azure / GCP**
- **Workflow Engines (Temporal, Camunda, Orkes Conductor)**
- **Real Production Implementations**
