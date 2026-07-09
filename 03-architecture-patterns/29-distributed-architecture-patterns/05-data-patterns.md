
# 5. Data Patterns

Data.

Is the heart.

Of every.

Distributed system.

Unfortunately.

Data is also.

The hardest part.

To distribute.

---

# Why Data Patterns Exist

In a monolith.

Life is simple.

```text
Application

↓

Single Database

↓

Single Transaction
```

Everything happens.

Inside.

One transaction.

---

In microservices.

Things change.

```text
Order Service

↓

Order Database

------------------------

Payment Service

↓

Payment Database

------------------------

Inventory Service

↓

Inventory Database
```

Now.

Questions appear.

- How do transactions work?
- How do services stay consistent?
- How do we synchronize data?
- How do we avoid distributed transactions?
- How do we recover from failures?

These problems.

Created.

Modern data patterns.

---

# Classification

```text
Data Patterns

│

├── CQRS

├── Event Sourcing

├── Saga

├── Outbox

├── Inbox

├── Materialized View

├── Data Replication

└── Event-Carried State
```

Each solves.

A different.

Data problem.

---

# Choosing a Data Pattern

Ask yourself.

```text
Need separate

Read and Write?

↓

CQRS

────────────────────

Need complete history?

↓

Event Sourcing

────────────────────

Need distributed transaction?

↓

Saga

────────────────────

Need reliable messaging?

↓

Outbox

────────────────────

Need exactly-once consumer?

↓

Inbox

────────────────────

Need fast queries?

↓

Materialized View
```

---

# 5.1 CQRS (Command Query Responsibility Segregation)

One of the most.

Popular.

Enterprise patterns.

---

# Problem

Traditional systems.

Use.

One model.

For.

Reading.

And.

Writing.

```text
Client

↓

Application

↓

Database
```

As systems grow.

Read traffic.

Often becomes.

Much larger.

Than write traffic.

---

Example.

Amazon.

```text
Product Views

↓

5 Million/min

--------------------

Orders

↓

5 Thousand/min
```

Same database.

Handles both.

Inefficient.

---

# Definition

> **CQRS separates the write model (Commands) from the read model (Queries), allowing each to evolve and scale independently.**

---

# Commands

Commands.

Change state.

Examples.

```text
Create Order

Update Customer

Cancel Booking

Transfer Money
```

Commands.

Never return.

Business data.

Usually.

Only.

Success.

Failure.

Or.

Identifier.

---

# Queries

Queries.

Read data.

Examples.

```text
Get Orders

Get Customer

Search Products

View Balance
```

Queries.

Never modify.

Business state.

---

# Architecture

```text
                 Client

                    │

          ┌─────────┴─────────┐

          ▼                   ▼

   Command API           Query API

          │                   │

          ▼                   ▼

     Write Model         Read Model

          │                   │

          ▼                   ▼

     Write Database      Read Database
```

Notice.

Separate.

Read.

And.

Write paths.

---

# Why Separate?

Write model.

Optimized for.

Business rules.

Validation.

Consistency.

---

Read model.

Optimized for.

Searching.

Reporting.

Filtering.

Dashboards.

---

# Business Example

Amazon Product Page.

Needs.

```text
Product

Inventory

Reviews

Ratings

Recommendations

Delivery ETA
```

Query model.

Can pre-build.

Exactly this.

Without affecting.

Order processing.

---

# Advantages

- Independent scaling
- Faster queries
- Better read performance
- Specialized databases
- Simpler write model

---

# Disadvantages

- More infrastructure
- Eventual consistency
- Data synchronization
- Higher operational complexity

---

# Real Companies

- Amazon
- Netflix
- Uber
- Microsoft
- Banking platforms

---

# 5.2 Event Sourcing

Perhaps.

The most misunderstood.

Architecture pattern.

---

# Traditional Storage

Most systems.

Store.

Current state.

Example.

```text
Account

Balance

₹12,500
```

Question.

How did.

Balance become.

₹12,500?

History lost.

---

# Event Sourcing

Instead.

Store.

Every event.

That happened.

```text
Deposit ₹500

Withdraw ₹200

Deposit ₹1000

Transfer ₹300
```

Current balance.

Is calculated.

By replaying.

Events.

---

# Definition

> **Event Sourcing stores every state change as an immutable event instead of storing only the latest state.**

---

# Architecture

```text
Client

↓

Command

↓

Business Logic

↓

Event Store

↓

Project Current State
```

---

# Example

Instead of.

```text
Balance

₹5000
```

Store.

```text
AccountCreated

↓

MoneyDeposited

↓

MoneyWithdrawn

↓

MoneyTransferred
```

History.

Never disappears.

---

# Benefits

- Complete audit history
- Time travel
- Replay
- Debugging
- Analytics
- Event publishing

---

# Challenges

- Complex implementation
- Schema evolution
- Event versioning
- Replay performance

---

# Typical Use Cases

- Banking
- Trading
- Insurance
- Healthcare
- Audit systems

---

# CQRS + Event Sourcing

These patterns.

Often work.

Together.

```text
Commands

↓

Event Store

↓

Events

↓

Read Model

↓

Queries
```

Write.

Produces events.

Read model.

Builds projections.

---

# 5.3 Saga Pattern

Microservices.

Cannot.

Use.

Traditional.

Database transactions.

Across services.

---

# Problem

Customer.

Places order.

```text
Order Service

↓

Payment Service

↓

Inventory Service

↓

Shipping Service
```

Payment succeeds.

Inventory fails.

How do we.

Rollback?

---

# Definition

> **Saga coordinates a business transaction across multiple services using a sequence of local transactions with compensating actions instead of a distributed database transaction.**

---

# Architecture

```text
Create Order

↓

Reserve Inventory

↓

Process Payment

↓

Arrange Shipping

↓

Success
```

If.

Shipping fails.

```text
Cancel Payment

↓

Release Inventory

↓

Cancel Order
```

---

# Compensating Transaction

Instead of.

Database rollback.

Business rollback.

Example.

```text
Reserve Seat

↓

Failure

↓

Release Seat
```

---

# Saga Types

```text
Saga

│

├── Choreography

└── Orchestration
```

We'll discuss.

These separately.

---

# Advantages

- No distributed transactions
- Independent services
- Highly scalable

---

# Challenges

- Compensation logic
- Event ordering
- Failure handling
- Monitoring

---

# 5.4 Outbox Pattern

One of the most.

Important.

Microservice patterns.

---

# Problem

Imagine.

Order Service.

Does this.

```text
Save Order

↓

Publish Kafka Event
```

Question.

What if.

Database commit succeeds.

But.

Kafka publish fails?

Now.

Order exists.

No event.

System inconsistent.

---

# Solution

Store.

The event.

Inside.

The same transaction.

---

# Architecture

```text
Database Transaction

↓

Orders Table

+

Outbox Table

↓

Commit
```

Later.

Background process.

Reads.

Outbox.

Publishes events.

---

# Flow

```text
Client

↓

Save Order

↓

Save Outbox

↓

Commit

↓

Publisher

↓

Kafka
```

Guaranteed.

No lost events.

---

# Benefits

- Reliable messaging
- Atomic write
- No distributed transaction

---

# 5.5 Inbox Pattern

Outbox.

Guarantees.

Reliable publishing.

Question.

What about.

Reliable consumption?

---

Messages.

May arrive.

Twice.

```text
Kafka

↓

Consumer

↓

Crash

↓

Kafka Retry

↓

Same Message Again
```

Duplicate processing.

Occurs.

---

# Solution

Inbox Pattern.

---

# Definition

> **Inbox stores processed message identifiers so duplicate messages can be safely ignored.**

---

# Architecture

```text
Consumer

↓

Inbox Table

↓

Already Processed?

↓

No

↓

Execute

↓

Save Message ID
```

---

Next time.

Same message.

Appears.

Ignored.

---

# Benefits

- Idempotent consumers
- Safe retries
- Exactly-once effect

---

# 5.6 Materialized View

Sometimes.

Queries.

Are too expensive.

---

Example.

Dashboard.

Needs.

```text
Orders

Payments

Inventory

Users

Revenue
```

Query.

Joins.

Millions.

Of rows.

Slow.

---

Solution.

Materialized View.

---

# Definition

> **A Materialized View stores precomputed query results that can be refreshed periodically or incrementally.**

---

Architecture.

```text
Events

↓

Projection

↓

Materialized View

↓

Dashboard
```

---

# Advantages

- Fast queries
- Better reporting
- Reduced joins

---

# Drawbacks

- Additional storage
- Refresh complexity
- Eventual consistency

---

# Pattern Relationships

These patterns.

Frequently work.

Together.

```text
Client

↓

Command

↓

CQRS

↓

Event Sourcing

↓

Outbox

↓

Kafka

↓

Inbox

↓

Materialized View

↓

Query API
```

Notice.

Modern systems.

Rarely use.

One pattern.

They combine.

Multiple patterns.

---

# Example — Amazon Checkout

```text
Customer

↓

Order Command

↓

Order Service

↓

Order Database

+

Outbox

↓

Kafka

↓

Inventory

↓

Payment

↓

Shipping

↓

Notification

↓

Materialized Views

↓

Customer Dashboard
```

One business flow.

Uses.

Nearly every.

Data pattern.

---

# Pattern Comparison

| Pattern | Primary Goal |
|----------|--------------|
| CQRS | Separate reads and writes |
| Event Sourcing | Preserve complete history |
| Saga | Distributed business transactions |
| Outbox | Reliable event publishing |
| Inbox | Reliable event consumption |
| Materialized View | Fast query performance |

---

# Common Misconceptions

### Misconception 1

> Every microservice needs CQRS.

False.

Simple CRUD services.

Usually.

Do not benefit.

From CQRS.

---

### Misconception 2

> Event Sourcing replaces databases.

False.

It changes.

How state.

Is persisted.

Most systems.

Still use databases.

---

### Misconception 3

> Saga guarantees ACID transactions.

False.

Saga provides.

Business consistency.

Through compensation.

Not.

Database rollback.

---

### Misconception 4

> Outbox guarantees exactly-once delivery.

False.

Outbox guarantees.

Reliable publishing.

Inbox and idempotent consumers.

Are still required.

---

# Interview Questions

### Q1

When should CQRS be used?

**Answer**

When read and write workloads have significantly different characteristics and need to scale or evolve independently.

---

### Q2

Why is Event Sourcing valuable for financial systems?

**Answer**

Because every state change is preserved as an immutable event, providing a complete audit trail and enabling replay.

---

### Q3

Why is the Outbox Pattern necessary?

**Answer**

To ensure that database changes and event publication occur reliably without requiring distributed transactions.

---

### Q4

What problem does the Inbox Pattern solve?

**Answer**

It prevents duplicate message processing by tracking previously processed message identifiers.

---

# Architect's Perspective

Data patterns solve one of the hardest challenges in distributed systems: **maintaining consistency without relying on distributed ACID transactions**.

Rather than forcing every service into a single transactional boundary, these patterns embrace asynchronous communication, eventual consistency, immutable events, and reliable messaging.

The strongest architectures rarely depend on one pattern alone. They combine **CQRS**, **Event Sourcing**, **Saga**, **Outbox**, **Inbox**, and **Materialized Views** to achieve scalability, resilience, auditability, and operational simplicity.

---

**End of Part 5**

The next part will cover **Messaging Patterns**, including:

- Event Bus
- Message Queue
- Competing Consumers
- Dead Letter Queue (DLQ)
- Retry Queue
- Priority Queue
- Request–Reply over Messaging
- Fan-Out
- Messaging Best Practices
