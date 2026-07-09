
# 21. Event Producers

> **An Event Producer is a system, application, or service that creates and publishes events when business activities occur.**

The producer is the source of business facts.

Examples:

- Order Service
- Payment Service
- Inventory Service
- User Service

The producer does **not** know who will consume the event.

---

# Example

Customer places an order.

Order Service publishes:

```text
OrderPlaced
```

It does **not** call:

- Email Service
- Inventory Service
- Analytics
- Shipping

Instead,

it simply publishes the event.

---

# Architecture

```text
Order Service

↓

Publish

↓

OrderPlaced
```

---

# Producer Responsibilities

A producer should:

- Create valid events
- Include required business data
- Publish successfully
- Avoid duplicate publishing
- Preserve event ordering when required

---

# Producer Should NOT

A producer should not know:

- Who consumes the event
- How many consumers exist
- What downstream services do

This keeps systems loosely coupled.

---

# Good Producer Example

```text
Order Service

↓

OrderPlaced
```

Simple.

Business-focused.

---

# Bad Producer Example

```text
Order Service

↓

Email Service

↓

Inventory

↓

CRM

↓

Marketing

↓

Analytics
```

Too many dependencies.

---

# 22. Event Consumers

> **An Event Consumer is a service that subscribes to events and performs business actions in response.**

Consumers react.

They never control the producer.

---

# Example

Consumer receives:

```
OrderPlaced
```

Processes:

- Reserve Inventory
- Send Email
- Generate Invoice
- Update Dashboard

Each consumer acts independently.

---

# Architecture

```text
Topic

↓

Inventory

↓

Shipping

↓

Email

↓

Analytics
```

---

# Consumer Responsibilities

Consumers should:

- Validate event
- Process idempotently
- Handle retries
- Acknowledge after success
- Support replay
- Publish new events when appropriate

---

# Consumer Independence

Consumers must remain:

Independent.

One consumer failure.

Should not stop.

Others.

---

# Example

Analytics unavailable.

Inventory.

Still processes.

Order.

Successfully.

---

# 23. Event Broker

> **An Event Broker receives, stores, routes, and delivers messages between producers and consumers.**

The broker is the communication hub.

---

# Architecture

```text
Producer

↓

Broker

↓

Consumer
```

Neither side knows the implementation details of the other.

---

# Broker Responsibilities

- Store messages
- Route messages
- Deliver reliably
- Handle retries
- Maintain ordering (where supported)
- Scale consumers
- Support durability

---

# Popular Brokers

- Apache Kafka
- RabbitMQ
- Apache Pulsar
- ActiveMQ Artemis
- Amazon SQS
- Azure Service Bus
- Google Pub/Sub
- NATS

Each is optimized for different workloads.

---

# Broker Benefits

Without broker.

```
N × M

Connections
```

Every service connects to every other service.

---

With broker.

```text
Services

↓

Broker

↓

Services
```

Far fewer dependencies.

---

# 24. Event Types

Not every event has the same purpose.

Enterprise systems usually distinguish several categories.

---

# Domain Events

> **Domain Events describe important business facts within a bounded context.**

Examples.

```
OrderPlaced

PaymentReceived

ShipmentCreated
```

These represent business concepts.

---

# Characteristics

- Business language
- Immutable
- Owned by one domain
- Meaningful to domain experts

---

# Example

E-Commerce.

```
OrderPlaced
```

Created by:

Order Service.

---

# Integration Events

> **Integration Events communicate important business facts between bounded contexts or external systems.**

Sometimes.

Internal domain events.

Should not be published externally.

Instead.

Transform.

Into integration events.

---

# Example

Domain Event.

```
OrderPlaced
```

↓

Integration Event.

```
CustomerOrderCreated
```

Contains only information other systems require.

---

# Why Separate Them?

Internal models evolve.

External contracts should remain stable.

Protects consumers from internal implementation changes.

---

# Event Notification

Sometimes.

Only notify.

That something happened.

Example.

```
ProductUpdated
```

Consumer decides.

Whether to fetch more information.

---

# Event-Carried State Transfer

Instead of only notifying.

Send.

Complete business data.

Example.

```json
{
  "orderId":100,
  "customerId":25,
  "total":1200
}
```

Consumers rarely need another API call.

---

# Comparison

| Pattern | Contains |
|----------|-----------|
| Notification | Minimal information |
| Event-Carried State | Business data |

---

# Trade-off

Notification.

Smaller messages.

More API calls.

---

State Transfer.

Larger messages.

Fewer network requests.

---

# 25. Event Choreography

> **In choreography, each service independently reacts to events without a central coordinator.**

No service controls others.

Everyone listens.

Everyone reacts.

---

# Architecture

```text
OrderPlaced

↓

Inventory

↓

InventoryReserved

↓

Shipping

↓

ShipmentCreated

↓

Notification
```

Events naturally flow.

---

# Advantages

- Loose coupling
- Independent deployment
- High scalability
- Simple ownership

---

# Challenges

Large systems.

Event chains become difficult.

To understand.

---

# Example

Order triggers.

Inventory.

↓

Payment.

↓

Shipping.

↓

Email.

↓

Analytics.

No central visibility.

---

# 26. Event Orchestration

> **In orchestration, a central coordinator directs each step of a business workflow.**

---

# Architecture

```text
Order Orchestrator

↓

Inventory

↓

Payment

↓

Shipping

↓

Notification
```

Coordinator decides.

Next action.

---

# Advantages

- Clear workflow
- Easier monitoring
- Easier debugging
- Better control

---

# Disadvantages

- Central dependency
- More coordination
- Possible bottleneck

---

# Choreography vs Orchestration

| Choreography | Orchestration |
|---------------|---------------|
| Distributed decisions | Central coordinator |
| Loose coupling | More centralized |
| Harder debugging | Easier workflow visibility |
| Better scalability | Better control |

---

# Which Should You Choose?

Simple independent events.

Use.

```
Choreography
```

---

Complex business workflow.

Use.

```
Orchestration
```

---

Long-running transactions.

Often.

Saga Orchestrator.

---

# Enterprise Example

Food Delivery.

Order.

↓

Restaurant Accepts.

↓

Driver Assigned.

↓

Food Prepared.

↓

Pickup.

↓

Delivery.

Often orchestrated.

---

Social Media.

Post Created.

↓

Search Index.

↓

Analytics.

↓

Notifications.

↓

Recommendations.

Often choreographed.

---

# 27. Event Versioning

Events evolve.

Applications evolve.

Consumers may not upgrade simultaneously.

---

# Problem

Version 1.

```json
{
 "id":100,
 "price":500
}
```

Later.

Need currency.

---

Version 2.

```json
{
 "id":100,
 "price":500,
 "currency":"USD"
}
```

Old consumers.

May fail.

---

# Versioning Strategies

- Additive changes
- Version field
- Topic versioning
- Schema Registry

---

# Additive Changes

Safest.

Only add.

New optional fields.

Avoid removing existing fields.

---

# Version Field

Example.

```json
{
 "version":2
}
```

Consumers understand.

Format.

---

# Topic Versioning

Example.

```
orders.v1

orders.v2
```

Old consumers.

Continue.

Using V1.

---

# Benefits

- Smooth migration
- Independent deployment
- Backward compatibility

---

# Challenges

Multiple versions.

Need support.

Temporarily.

---

# 28. Schema Evolution

> **Schema evolution allows event formats to change over time without breaking existing producers or consumers.**

Large enterprises.

Cannot.

Upgrade.

Every service.

Simultaneously.

---

# Compatible Changes

Good.

- Add optional field
- Increase enum values carefully
- Add new event type

---

# Breaking Changes

Bad.

- Remove mandatory field
- Rename field
- Change data type
- Change semantic meaning

---

# Example

Bad.

```
price

↓

amount
```

Consumers fail.

---

Better.

```
price

↓

price

+

amount
```

Deprecate later.

---

# Schema Compatibility

Common compatibility modes.

- Backward
- Forward
- Full

---

## Backward Compatible

New consumers.

Read old events.

---

## Forward Compatible

Old consumers.

Read new events.

---

## Full Compatible

Both directions.

Safest.

---

# Schema Registry

Large organizations often maintain:

A central registry.

Containing.

- Event schemas
- Versions
- Compatibility rules

---

Popular Technologies

- Confluent Schema Registry
- AWS Glue Schema Registry
- Apicurio Registry

---

# Serialization Formats

Common event formats.

- JSON
- Apache Avro
- Protocol Buffers (Protobuf)
- Apache Thrift

---

# Comparison

| Format | Advantages | Trade-offs |
|---------|------------|------------|
| JSON | Human-readable | Larger payloads |
| Avro | Compact, schema evolution | Requires schema management |
| Protobuf | Compact, fast | Binary format, tooling required |
| Thrift | Cross-language support | Less common in modern event streaming |

---

# Event Design Best Practices

Design events using business language.

Good.

```
OrderPlaced
```

Bad.

```
InsertIntoOrdersTable
```

---

Include.

- Event ID
- Timestamp
- Version
- Producer
- Correlation ID
- Business payload

---

Keep Events Immutable

Never modify.

Published events.

Create.

New events.

Instead.

---

Example.

Bad.

```
OrderPlaced

↓

Modify Existing Event
```

Good.

```
OrderPlaced

↓

OrderUpdated

↓

OrderCancelled
```

History preserved.

---

# Common Mistakes

### Publishing Database Events

Avoid exposing table names and internal schema.

Publish business events instead.

---

### Tight Consumer Assumptions

Consumers should tolerate additional optional fields and unknown metadata.

---

### Large Event Payloads

Do not publish entire database records when only a few fields are required.

Balance completeness with message size.

---

### Breaking Schema Changes

Removing or renaming fields without migration breaks downstream consumers.

Plan schema evolution carefully.

---

# Architect's Perspective

Events represent the **language of the business**.

Well-designed events outlive:

- Programming languages
- Frameworks
- Databases
- Individual services

Architects should therefore design events as **stable business contracts**, not as serialized Java objects or database rows.

The most successful event-driven systems:

- Publish business facts.
- Keep events immutable.
- Separate internal domain models from integration contracts.
- Evolve schemas without breaking consumers.
- Choose choreography or orchestration based on business workflow complexity rather than technology preference.

Thinking in **business events** instead of **technical operations** is one of the defining characteristics of modern enterprise architecture.

---

**End of Part 4**

The next part will cover:

- **Apache Kafka**
- **RabbitMQ**
- **Apache Pulsar**
- **ActiveMQ Artemis**
- **Amazon SQS**
- **Amazon SNS**
- **Azure Service Bus**
- **Google Pub/Sub**
- **NATS**
- **Messaging Platform Selection Guide**
