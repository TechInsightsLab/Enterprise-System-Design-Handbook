
# 4. Communication Patterns

Building.

Microservices.

Is only.

Half.

The challenge.

The other half.

Is.

How services.

Communicate.

---

# Why Communication Patterns Matter

Imagine.

An e-commerce platform.

Contains.

```text
Order Service

Payment Service

Inventory Service

Shipping Service

Notification Service
```

Question.

How should.

These services.

Talk.

To each other?

Should.

They.

Call each other.

Directly?

Should.

They use.

Kafka?

RabbitMQ?

REST?

gRPC?

Events?

Every choice.

Has.

Trade-offs.

---

# Communication Is the Backbone

Every distributed system.

Consists of.

Two things.

```text
Services

+

Communication
```

Poor communication.

Leads to.

- Tight coupling
- Cascading failures
- High latency
- Low scalability

---

# Classification

Communication patterns.

Can be grouped.

Into.

```text
Communication Patterns

│

├── Synchronous

├── Asynchronous

├── Event-Based

├── Request Aggregation

└── Broadcast Patterns
```

Each solves.

Different problems.

---

# Choosing the Right Pattern

Before selecting.

Any communication mechanism.

Ask.

```text
Does the caller

need

an immediate response?

        │

     Yes │ No

        ▼

Synchronous

        │

────────┼────────

        ▼

Asynchronous
```

This is.

Usually.

The first.

Architecture decision.

---

# 4.1 Synchronous Communication

## Definition

> **In synchronous communication, the caller waits until the receiver finishes processing and returns a response.**

---

# Example

Customer.

Places an order.

```text
Client

↓

Order Service

↓

Payment Service

↓

Response

↓

Client
```

Order Service.

Cannot continue.

Until.

Payment responds.

---

# Characteristics

- Blocking
- Immediate response
- Request/Reply
- Simpler programming model
- Stronger consistency

---

# Typical Technologies

- REST
- HTTP
- HTTPS
- gRPC
- GraphQL
- Thrift

---

# Business Example

ATM.

Withdraws money.

```text
ATM

↓

Bank Service

↓

Balance Check

↓

Approve

↓

Cash Dispensed
```

ATM.

Must know.

Immediately.

Whether.

Withdrawal succeeded.

---

# Advantages

- Simple
- Easy debugging
- Immediate feedback
- Strong consistency

---

# Disadvantages

- Tight coupling
- Higher latency
- Cascading failures
- Lower resilience

---

# 4.2 Asynchronous Communication

## Definition

> **In asynchronous communication, the sender does not wait for the receiver to finish processing.**

---

# Architecture

```text
Order Service

↓

Message Broker

↓

Inventory Service

↓

Shipping Service

↓

Notification Service
```

Order Service.

Continues.

Immediately.

---

# Characteristics

- Non-blocking
- Decoupled
- High scalability
- Better resilience

---

# Business Example

Customer.

Places an order.

System publishes.

```text
OrderCreated
```

Immediately.

Order Service.

Returns.

Success.

Later.

Inventory.

Shipping.

Notifications.

Process independently.

---

# Technologies

- Kafka
- RabbitMQ
- ActiveMQ
- Pulsar
- AWS SQS
- Google Pub/Sub
- Azure Service Bus

---

# Advantages

- Loose coupling
- Independent scaling
- High throughput
- Failure isolation

---

# Disadvantages

- Eventual consistency
- Harder debugging
- Duplicate messages
- Ordering challenges

---

# Synchronous vs Asynchronous

| Feature | Synchronous | Asynchronous |
|----------|-------------|--------------|
| Caller Waits | Yes | No |
| Response Time | Immediate | Later |
| Coupling | High | Low |
| Scalability | Lower | Higher |
| Failure Isolation | Lower | Higher |
| Typical Use | Queries | Background Processing |

---

# When to Use Synchronous

Good examples.

- Login
- Payment authorization
- Balance inquiry
- OTP validation
- User authentication

Need.

Immediate answer.

---

# When to Use Asynchronous

Good examples.

- Email
- SMS
- Notifications
- Analytics
- Search indexing
- Recommendation engine
- Audit logs

Response.

Can happen.

Later.

---

# 4.3 Request–Reply Pattern

One of the oldest.

Communication patterns.

---

## Definition

> **One service sends a request and expects exactly one response.**

---

Architecture.

```text
Service A

↓

Request

↓

Service B

↓

Response

↓

Service A
```

---

Typical.

REST.

gRPC.

GraphQL.

Follow.

This pattern.

---

# Business Example

```text
Order Service

↓

Payment Service

↓

Payment Status
```

Exactly.

One request.

Exactly.

One response.

---

# Advantages

- Easy
- Predictable
- Simple contracts

---

# Disadvantages

- Blocking
- Timeouts
- Cascading failures

---

# 4.4 Publish–Subscribe (Pub/Sub)

One of the most important.

Distributed patterns.

---

## Definition

> **Publish–Subscribe allows producers to publish events without knowing who will consume them.**

---

Architecture.

```text
Publisher

↓

Topic

↓

Subscriber A

Subscriber B

Subscriber C
```

Publisher.

Never knows.

Who consumes.

The event.

---

# Business Example

Customer.

Places an order.

Publisher.

Sends.

```text
OrderCreated
```

Subscribers.

```text
Inventory

Billing

Shipping

Notifications

Analytics
```

Each reacts.

Independently.

---

# Benefits

Order Service.

Never changes.

When.

New consumers.

Appear.

---

# Real Technologies

- Kafka
- Pulsar
- Google Pub/Sub
- RabbitMQ (topic exchange)
- AWS SNS

---

# Advantages

- Loose coupling
- Independent evolution
- Excellent scalability
- Event-driven architecture

---

# Disadvantages

- Event ordering
- Duplicate delivery
- Event versioning
- Monitoring complexity

---

# 4.5 Event Notification Pattern

Sometimes.

Consumers.

Only need.

To know.

Something happened.

---

Example.

```text
OrderCreated
```

Contains.

```text
OrderId
Timestamp
```

Nothing more.

Consumers.

Fetch.

Additional information.

If required.

---

# Advantages

- Small messages
- Low bandwidth
- Simple events

---

# Disadvantages

Consumers.

Often perform.

Additional API calls.

---

# 4.6 Event-Carried State Transfer

Instead of.

Sending.

Only.

Order ID.

The event.

Contains.

Complete business state.

---

Example.

```text
OrderCreated

{

OrderId

Customer

Items

Price

Shipping

Coupon

Address

}
```

Consumers.

Already have.

Everything needed.

---

# Benefits

No additional.

Network calls.

---

# Drawbacks

Large events.

Schema evolution.

Higher storage.

---

# Event Notification vs Event-Carried State

| Event Notification | Event-Carried State |
|-------------------|---------------------|
| Small event | Rich event |
| Consumer fetches data | Consumer already has data |
| Lower bandwidth | Higher bandwidth |
| More API calls | Fewer API calls |

---

# 4.7 Request Aggregation Pattern

Clients.

Often need.

Information.

From multiple services.

---

Example.

Product Page.

Needs.

```text
Product

Inventory

Price

Reviews

Recommendations
```

Calling.

Five services.

From browser.

Is inefficient.

---

Solution.

Aggregation Service.

```text
Client

↓

Aggregator

↓

Product

Inventory

Reviews

Price

↓

Single Response
```

---

# Advantages

- Reduced client complexity
- Fewer network calls
- Better latency

---

# Common Uses

- API Gateway
- BFF
- GraphQL
- Dashboard APIs

---

# 4.8 Scatter–Gather Pattern

Need.

Responses.

From.

Multiple services.

In parallel.

---

Architecture.

```text
Aggregator

↓

Search A

Search B

Search C

↓

Combine Results

↓

Client
```

---

Business Example

Travel website.

Needs.

```text
Flights

Hotels

Rental Cars
```

All searched.

Simultaneously.

---

# Benefits

Parallel execution.

Lower latency.

---

# Challenges

- Partial failures
- Timeouts
- Response merging
- Duplicate data

---

# Communication Pattern Decision Matrix

| Requirement | Pattern |
|-------------|----------|
| Immediate answer | Synchronous |
| Background processing | Asynchronous |
| Notify many consumers | Pub/Sub |
| Simple API | Request–Reply |
| Dashboard | Request Aggregation |
| Parallel search | Scatter–Gather |
| Small events | Event Notification |
| Rich events | Event-Carried State |

---

# Combining Patterns

Real systems.

Rarely use.

Only one.

Example.

Amazon Checkout.

```text
Customer

↓

API Gateway

↓

Order Service

↓

Payment Service (Sync)

↓

Publish OrderCreated (Async)

↓

Inventory

Shipping

Notifications

Analytics
```

Multiple patterns.

Work together.

---

# Common Misconceptions

### Misconception 1

> Asynchronous communication is always better.

False.

If the business.

Needs.

An immediate decision.

Use synchronous communication.

---

### Misconception 2

> REST cannot scale.

False.

REST scales extremely well.

When used appropriately.

Problems usually arise from poor architecture.

Not the protocol itself.

---

### Misconception 3

> Every event should contain the full business object.

False.

Choose between Event Notification and Event-Carried State Transfer based on consumer needs and bandwidth trade-offs.

---

### Misconception 4

> One communication pattern should be used everywhere.

False.

Distributed systems combine multiple communication patterns depending on business requirements.

---

# Interview Questions

### Q1

When should synchronous communication be preferred over asynchronous communication?

**Answer**

When the caller requires an immediate response, such as authentication, payment authorization, or balance verification.

---

### Q2

Why is Publish–Subscribe loosely coupled?

**Answer**

Publishers do not know who the subscribers are. New consumers can subscribe without changing the publisher.

---

### Q3

When is Event-Carried State Transfer preferable?

**Answer**

When consumers require most or all of the event data and additional API calls would increase latency or coupling.

---

### Q4

What is the difference between Request Aggregation and Scatter–Gather?

**Answer**

Request Aggregation combines responses into one API for the client, while Scatter–Gather focuses on executing multiple requests in parallel and merging their results.

---

# Architect's Perspective

Communication patterns determine **how independently services can evolve**.

Choosing between synchronous and asynchronous communication is not a technology decision—it is a business decision driven by latency requirements, consistency needs, failure tolerance, and scalability.

The best distributed systems intentionally combine multiple communication patterns, selecting the simplest approach that satisfies the business requirement rather than forcing a single style across the entire architecture.

---

**End of Part 4**

The next part will cover **Data Patterns**, including:

- CQRS
- Event Sourcing
- Saga
- Outbox Pattern
- Inbox Pattern
- Materialized Views
- Transactional Messaging
- Data synchronization patterns
