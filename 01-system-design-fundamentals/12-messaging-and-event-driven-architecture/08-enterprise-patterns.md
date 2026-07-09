
# 59. Enterprise Messaging Patterns

As organizations adopt Event-Driven Architecture,

simple messaging is no longer enough.

Enterprise systems must solve problems like:

- Database consistency
- Duplicate messages
- Service failures
- Long-running transactions
- Event replay
- Cross-service communication

This section covers the most important enterprise messaging patterns.

---

# 60. Transactional Outbox Pattern

> **The Transactional Outbox Pattern guarantees that database updates and event publishing remain consistent by first storing events in the same database transaction before publishing them asynchronously.**

This is one of the most important patterns in modern microservices.

---

# The Dual Write Problem

Imagine Order Service.

Workflow:

```text
Save Order

↓

Publish OrderPlaced Event
```

Looks simple.

---

But consider failure.

```text
Save Order

✅ Success

↓

Publish Event

❌ Failed
```

Database contains:

Order.

Other services never know.

System becomes inconsistent.

---

Another scenario.

```text
Save Order

❌ Failed

↓

Publish Event

✅ Success
```

Consumers believe.

Order exists.

Database says otherwise.

Again.

Inconsistent.

---

# Why This Happens

Database and Message Broker are:

Two different systems.

No shared transaction.

---

# Incorrect Architecture

```text
Application

├── Database

└── Kafka
```

One succeeds.

One fails.

No coordination.

---

# Outbox Solution

Instead of publishing directly.

Store event.

Inside database.

---

# Architecture

```text
Application

↓

Database Transaction

├── Orders Table

└── Outbox Table

↓

Commit

↓

Outbox Publisher

↓

Kafka
```

Now.

Database and Outbox.

Commit together.

---

# Outbox Table Example

| Event ID | Aggregate | Event Type | Status |
|-----------|-----------|------------|--------|
| E100 | Order100 | OrderPlaced | NEW |
| E101 | Order101 | OrderPlaced | NEW |

---

# Workflow

Step 1.

Begin transaction.

---

Step 2.

Insert order.

---

Step 3.

Insert outbox record.

---

Step 4.

Commit transaction.

---

Step 5.

Background publisher.

Reads outbox.

Publishes event.

Marks as processed.

---

# Architecture Diagram

```text
Order API

↓

Database

├── Orders

└── Outbox

↓

Outbox Publisher

↓

Kafka

↓

Consumers
```

---

# Benefits

- No lost events
- No inconsistent state
- Reliable publishing
- Easy retry
- Simple recovery

---

# Challenges

Need.

Background publisher.

Need cleanup.

Outbox table grows.

---

# Enterprise Example

Amazon.

Customer places order.

Order committed.

Outbox written.

Publisher sends.

```
OrderPlaced
```

Even if Kafka is temporarily unavailable.

Order remains safe.

---

# 61. Inbox Pattern

> **The Inbox Pattern ensures that consumers process each incoming event only once, even if the broker delivers duplicate messages.**

The Outbox Pattern protects producers.

Inbox protects consumers.

---

# Why?

Messaging systems often provide:

At-Least-Once Delivery.

Duplicates happen.

Consumers must tolerate them.

---

# Architecture

```text
Kafka

↓

Consumer

↓

Inbox Table

↓

Business Processing
```

---

# Workflow

Receive event.

↓

Check inbox.

↓

Already processed?

↓

Yes.

Ignore.

↓

No.

Process.

↓

Store event ID.

---

# Inbox Table

| Event ID | Processed Time |
|-----------|----------------|
| E100 | 10:10 |
| E101 | 10:12 |

---

# Benefits

- Idempotency
- Safe retries
- Duplicate prevention

---

# Trade-offs

Extra storage.

Additional lookup.

Worth it for business-critical systems.

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

# 62. Saga Pattern Integration

Large business transactions.

Cannot use.

Distributed database transactions.

Instead.

Use.

Saga.

---

# Example

Travel Booking.

Need.

- Flight
- Hotel
- Car

Different services.

---

# Workflow

```text
Book Trip

↓

Reserve Flight

↓

Reserve Hotel

↓

Reserve Car
```

One service fails.

Need compensation.

---

# Messaging Integration

```text
Flight Reserved

↓

Hotel Reserved

↓

Car Failed

↓

Cancel Flight

↓

Cancel Hotel
```

Everything coordinated.

Using events.

---

# Choreography-Based Saga

Each service.

Publishes events.

Others react.

---

Architecture.

```text
OrderPlaced

↓

Inventory

↓

InventoryReserved

↓

Payment

↓

PaymentCompleted

↓

Shipping
```

No central controller.

---

# Orchestration-Based Saga

Central coordinator.

Controls workflow.

---

Architecture.

```text
Saga Orchestrator

↓

Inventory

↓

Payment

↓

Shipping
```

Clear visibility.

---

# Choosing Between Them

| Choreography | Orchestration |
|--------------|---------------|
| Loosely coupled | Central workflow |
| Better scalability | Better control |
| Harder debugging | Easier monitoring |

---

# 63. CQRS Integration

CQRS.

Command Query Responsibility Segregation.

Separates.

Writes.

From.

Reads.

Messaging connects both models.

---

# Architecture

```text
Command

↓

Database

↓

Publish Event

↓

Read Model

↓

Query Database
```

---

# Example

Order Created.

↓

Kafka.

↓

Search Index.

↓

Dashboard.

↓

Analytics.

Each read model updates independently.

---

# Benefits

- Independent scaling
- Optimized read models
- Better performance
- Event replay

---

# 64. Change Data Capture (CDC)

> **CDC captures database changes and converts them into events without modifying application code.**

---

# Traditional Approach

Application.

Updates database.

Publishes event.

Developer responsible.

---

# CDC Approach

Application.

Updates database.

CDC detects.

Change.

Publishes event.

Automatically.

---

# Architecture

```text
Application

↓

Database

↓

Transaction Log

↓

CDC

↓

Kafka
```

---

# Popular Technologies

- Debezium
- Oracle GoldenGate
- SQL Server CDC
- AWS DMS
- PostgreSQL Logical Replication

---

# Example

Customer Address Updated.

CDC publishes.

```
CustomerUpdated
```

No application code required.

---

# Benefits

- Automatic
- Reliable
- Minimal application changes
- Supports legacy systems

---

# Trade-offs

- Operational complexity
- Database log management
- Event design still required

---

# Outbox vs CDC

| Outbox | CDC |
|---------|-----|
| Application writes event | Database log generates event |
| Business event | Data change event |
| More control | Less application code |

Many organizations combine both patterns.

---

# 65. Event Sourcing

> **Event Sourcing stores every state change as an immutable event instead of storing only the latest state.**

Instead of storing:

Current balance.

Store.

Every transaction.

---

# Traditional Database

```text
Account

Balance

₹10,000
```

Current state only.

---

# Event Sourcing

```text
AccountCreated

↓

MoneyDeposited

↓

MoneyWithdrawn

↓

InterestApplied
```

Entire history preserved.

---

# Current State

Reconstructed.

By replaying.

Events.

---

# Architecture

```text
Commands

↓

Events

↓

Event Store

↓

Read Models
```

---

# Benefits

- Complete audit history
- Replay
- Temporal queries
- Debugging
- Compliance

---

# Challenges

- Increased complexity
- Event evolution
- Snapshot management
- Learning curve

---

# Enterprise Uses

- Banking
- Trading
- Insurance
- Healthcare
- Audit-heavy systems

---

# Event Replay

Kafka enables replay.

Consumers restart.

Read events again.

---

# Example

Analytics Service.

Bug fixed.

Replay.

```
7 Days

Of Events
```

Rebuild reports.

Without touching production systems.

---

# Benefits

- Recovery
- Reprocessing
- New features
- Machine learning
- Auditing

---

# 66. Schema Registry

Large organizations.

Cannot rely.

On.

Implicit JSON structures.

Need governance.

---

> **A Schema Registry centrally manages event schemas, versions, and compatibility rules.**

---

# Architecture

```text
Producer

↓

Schema Registry

↓

Kafka

↓

Consumer
```

---

# Responsibilities

- Store schemas
- Validate producers
- Validate consumers
- Track versions
- Enforce compatibility

---

# Popular Registries

- Confluent Schema Registry
- AWS Glue Schema Registry
- Apicurio Registry

---

# Benefits

- Safer deployments
- Version control
- Consumer protection
- Better governance

---

# 67. Apache Avro

Avro.

Binary serialization.

Designed for Kafka.

---

# Characteristics

- Compact
- Fast
- Supports schema evolution
- Requires schema

---

# Why Better Than JSON?

JSON.

```json
{
 "id":100,
 "name":"Laptop"
}
```

Field names repeated.

---

Avro.

Stores schema separately.

Messages smaller.

---

# Benefits

- Lower bandwidth
- Faster processing
- Schema validation

---

# 68. Protocol Buffers (Protobuf)

Developed by Google.

Binary format.

---

# Benefits

- Extremely compact
- Cross-language
- High performance
- Strong tooling

---

# JSON vs Avro vs Protobuf

| Format | Human Readable | Size | Schema Evolution |
|----------|----------------|------|------------------|
| JSON | Yes | Large | Manual |
| Avro | No | Small | Excellent |
| Protobuf | No | Very Small | Excellent |

---

# Choosing a Format

Use JSON when:

- Simplicity matters
- Debugging is important
- Interoperability is the priority

Use Avro when:

- Kafka
- Large event streams
- Schema Registry

Use Protobuf when:

- High performance
- gRPC
- Internal services

---

# Enterprise Integration Flow

```text
Order API

↓

Orders DB

↓

Outbox

↓

Kafka

↓

Schema Registry

↓

Consumers

↓

Inbox

↓

Business Processing
```

This combines:

- Reliability
- Versioning
- Idempotency
- Governance

---

# Common Mistakes

### Publishing Directly After Database Commit

Dual-write problem.

Use Outbox for critical business events.

---

### Consumers Without Idempotency

Duplicate deliveries are expected.

Inbox or equivalent deduplication protects business logic.

---

### Unversioned Schemas

Breaking changes affect downstream services.

Adopt versioning and compatibility rules early.

---

### Using Event Sourcing Everywhere

Event Sourcing is powerful but complex.

Use it where auditability and replay provide clear business value.

---

### Treating CDC as Business Events

Database changes are not always meaningful business events.

Design integration events intentionally.

---

# Architect's Perspective

Enterprise messaging is not only about moving messages.

It is about preserving **business correctness** across distributed systems.

Patterns such as:

- Transactional Outbox
- Inbox
- Saga
- CQRS
- CDC
- Event Sourcing

exist because distributed systems cannot rely on traditional ACID transactions across independent services.

The architect's responsibility is to design systems where:

- Business events are never lost.
- Duplicate processing does not corrupt state.
- Services evolve independently.
- Historical events remain available when needed.
- Integration contracts remain stable over time.

These patterns form the backbone of modern event-driven enterprise architectures.

---

**End of Part 7**

The next part will cover:

- **Poison Messages**
- **Backpressure**
- **Consumer Lag Recovery**
- **Retry Strategies**
- **Dead Letter Queue Processing**
- **Replay Strategies**
- **Ordering Problems**
- **Partition Imbalance**
- **Failure Recovery**
- **Production Resilience**
