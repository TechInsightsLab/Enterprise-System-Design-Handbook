
# 3. Service Decomposition Patterns

One of the biggest.

Architecture decisions.

An organization.

Will ever make.

Is deciding.

When.

A single application.

Should become.

Multiple services.

---

# Why Service Decomposition Exists

Imagine.

An e-commerce application.

Initially.

Everything.

Lives.

Inside.

One application.

```text
                E-Commerce Application

┌────────────────────────────────────────────────────┐

 Customers

 Orders

 Payments

 Inventory

 Shipping

 Notifications

 Authentication

 Search

 Reviews

 Recommendations

 Reporting

└────────────────────────────────────────────────────┘

                    Single Database
```

Everything.

Works well.

---

After.

A few years.

Business grows.

Problems appear.

- 500 developers
- Daily deployments
- Millions of users
- Hundreds of production releases
- Long testing cycles
- One bug blocks entire deployment

Now.

The architecture.

Must evolve.

---

# Business Motivation

Suppose.

Black Friday.

Traffic arrives.

```text
Orders

↑ 20x

Payments

↑ 20x

Inventory

↑ 20x

Search

↑ 100x

Recommendations

↑ 300x
```

Question.

Should.

The entire application.

Scale?

Or only.

Search.

And.

Recommendations?

Scaling.

Everything.

Is expensive.

---

# Definition

> **Service Decomposition is the process of splitting a large application into smaller autonomous services, each responsible for a specific business capability.**

Notice.

Business capability.

Not.

Technical layer.

---

# Goals of Service Decomposition

```text
Service Decomposition

│

├── Independent Development

├── Independent Deployment

├── Independent Scaling

├── Fault Isolation

├── Faster Delivery

├── Team Ownership

└── Technology Flexibility
```

---

# Monolith vs Microservices

## Monolith

```text
Application

│

├── Orders

├── Payments

├── Inventory

├── Users

├── Shipping

└── Notifications
```

Single deployment.

Single database.

Single runtime.

---

## Microservices

```text
                API Gateway

                     │

      ┌──────────────┼──────────────┐

      ▼              ▼              ▼

 Order Service   Payment Service   User Service

      ▼              ▼              ▼

 Order DB       Payment DB       User DB

             ...

 Inventory Service

 Inventory DB
```

Every service.

Owns.

Its business capability.

And.

Its own data.

---

# Why Not Start With Microservices?

Many teams.

Make this mistake.

Day 1.

They create.

```text
20 Services

↓

2 Developers
```

Result.

```text
Distributed Monolith
```

Instead of.

Scalable architecture.

They create.

Distributed complexity.

---

# Architect's Rule

> **Start with a well-designed Modular Monolith. Move to Microservices only when business demands independent evolution.**

This principle.

Has been followed.

By.

Amazon.

Netflix.

Uber.

Spotify.

Shopify.

LinkedIn.

And many others.

---

# When Should You Split a Service?

Never ask.

```text
Can I split it?
```

Instead ask.

```text
Should I split it?
```

---

# Indicators

Split.

When.

Business requires.

- Independent deployment
- Independent scaling
- Independent ownership
- Independent release cycles
- Independent technology

---

Don't split.

Because.

Microservices are fashionable.

---

# The Biggest Mistake

Wrong decomposition.

```text
User Controller Service

User Repository Service

User Validation Service

User Utility Service
```

This is.

Layer decomposition.

Not.

Business decomposition.

---

Correct decomposition.

```text
User Service

Order Service

Inventory Service

Payment Service

Shipping Service
```

Each.

Represents.

A business capability.

---

# Domain-Driven Decomposition

Modern architectures.

Usually.

Use.

**Domain-Driven Design (DDD).**

To identify.

Service boundaries.

---

# What is a Domain?

A domain.

Represents.

The business.

The software solves.

Example.

E-commerce.

Contains.

```text
Users

Orders

Inventory

Payments

Shipping

Pricing

Recommendations

Reviews
```

Each.

Is a domain.

---

# Bounded Context

One of the most.

Important concepts.

In DDD.

---

## Definition

> **A Bounded Context defines a clear boundary within which a particular business model, terminology, and data are valid.**

Inside.

A bounded context.

Everything.

Has.

One meaning.

---

# Example

Consider.

The word.

```text
Customer
```

---

In.

Order Service.

Customer means.

```text
Shipping Address

Billing Address

Order History
```

---

In.

Marketing Service.

Customer means.

```text
Campaign

Preferences

Segments

Coupons
```

---

In.

Support Service.

Customer means.

```text
Tickets

Complaints

Call History
```

Same word.

Different meanings.

Different bounded contexts.

---

# Architecture

```text
                 E-Commerce Domain

──────────────────────────────────────────

 User Context

──────────────────────────────────────────

 Order Context

──────────────────────────────────────────

 Inventory Context

──────────────────────────────────────────

 Payment Context

──────────────────────────────────────────

 Shipping Context
```

Every context.

Can become.

One microservice.

---

# One Service = One Business Capability

Good microservices.

Usually answer.

Exactly one question.

---

Payment Service.

```text
Handles payments.
```

Inventory Service.

```text
Manages stock.
```

Shipping Service.

```text
Delivers orders.
```

If a service.

Answers.

Too many questions.

It is.

Too large.

---

# API Gateway Pattern

Once.

Applications.

Become.

Microservices.

Clients.

Should not.

Call.

Every service.

Directly.

---

# Problem

Without API Gateway.

```text
Mobile App

↓

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

Client.

Needs.

To understand.

Every service.

Very tightly coupled.

---

# Solution

Introduce.

An API Gateway.

```text
                 Mobile App

                      │

                 API Gateway

        ┌─────────────┼─────────────┐

        ▼             ▼             ▼

   Orders       Payments      Inventory
```

Clients.

Know.

Only.

The gateway.

---

# Responsibilities

API Gateway.

May provide.

- Routing
- Authentication
- Authorization
- SSL termination
- Rate limiting
- Aggregation
- Request transformation
- API versioning
- Monitoring

---

# Advantages

- Simple client
- Centralized security
- Request aggregation
- Easier API evolution

---

# Disadvantages

- Additional network hop
- Possible bottleneck
- Single operational component

Therefore.

API Gateways.

Should be.

Highly available.

---

# Backend for Frontend (BFF)

Sometimes.

Different clients.

Need.

Different APIs.

---

Example.

```text
Web

Needs

20 fields

---------------------

Mobile

Needs

5 fields

---------------------

Smart Watch

Needs

2 fields
```

One API.

Cannot optimize.

For all.

---

Solution.

```text
          Clients

      ┌────┼─────┐

      ▼    ▼     ▼

 Web BFF Mobile BFF Watch BFF

      │    │      │

      └────┼──────┘

        Backend Services
```

Each client.

Gets.

An optimized API.

---

# Why BFF?

Without BFF.

Mobile.

Downloads.

Large payloads.

Unnecessary.

Network cost.

Battery usage.

Latency.

Increase.

---

# Database per Service Pattern

One of the most.

Important.

Microservice rules.

---

## Definition

> **Each microservice owns its database. No other service accesses it directly.**

---

Wrong.

```text
Order Service

↓

Payment Database
```

Never.

Access.

Another service's tables.

---

Correct.

```text
Order Service

↓

Order Database

-------------------

Payment Service

↓

Payment Database
```

Communication.

Occurs.

Through APIs.

Or events.

---

# Why?

If.

Every service.

Uses.

One shared database.

You create.

A.

Distributed Monolith.

---

Benefits.

- Independent schema
- Independent deployment
- Better encapsulation
- Team ownership
- Technology freedom

---

# Challenges

Database per Service.

Introduces.

New problems.

- Distributed transactions
- Data duplication
- Eventual consistency
- Reporting
- Cross-service joins

These problems.

Will be solved.

Later.

Using patterns like.

- Saga
- CQRS
- Outbox
- Event Sourcing

---

# Service Granularity

One of the hardest.

Architecture decisions.

---

Too Large.

```text
Commerce Service
```

Contains.

Everything.

---

Too Small.

```text
Address Service

PostalCode Service

Country Service

City Service
```

Hundreds.

Of tiny services.

Massive.

Network overhead.

---

Correct.

Business capability.

```text
Order Service

Payment Service

Inventory Service

Shipping Service
```

---

# Evolution Path

```text
Layered Architecture

↓

Modular Monolith

↓

Bounded Contexts

↓

Microservices

↓

API Gateway

↓

Database per Service

↓

Event-Driven Architecture
```

Notice.

Microservices.

Are.

Not.

The destination.

They are.

One step.

In architectural evolution.

---

# Common Misconceptions

### Misconception 1

> Every module should become a microservice.

False.

Many modules.

Never require.

Independent deployment.

---

### Misconception 2

> Every microservice needs its own programming language.

False.

Polyglot architecture.

Should solve.

Business problems.

Not satisfy curiosity.

---

### Misconception 3

> Database per Service means one database server per service.

False.

Multiple schemas.

Or logical databases.

May share.

The same database cluster.

Ownership.

Is the important principle.

---

### Misconception 4

> API Gateway replaces service-to-service communication.

False.

API Gateway.

Primarily handles.

External client traffic.

Internal services.

Often communicate.

Directly.

Or through messaging.

---

# Interview Questions

### Q1

Why shouldn't multiple microservices share the same database schema?

**Answer**

Shared schemas create tight coupling, prevent independent evolution, complicate deployments, and effectively turn microservices into a distributed monolith.

---

### Q2

When should a monolith be decomposed into microservices?

**Answer**

When business capabilities need independent deployment, scaling, ownership, or release cycles—not simply because the application has become large.

---

### Q3

What is a Bounded Context?

**Answer**

A clearly defined boundary within which a specific business model and terminology are consistent. It is often the best starting point for defining microservice boundaries.

---

### Q4

What is the difference between an API Gateway and a BFF?

**Answer**

An API Gateway provides common cross-cutting capabilities for external clients, while a BFF is tailored to the needs of a specific client type (e.g., web, mobile, smartwatch).

---

# Architect's Perspective

Successful service decomposition is driven by **business boundaries, not technical layers**.

The objective is not to maximize the number of services, but to maximize **business autonomy** while minimizing operational complexity.

Architects who decompose systems around **bounded contexts**, **independent ownership**, and **clear service contracts** build systems that can evolve for years without constant restructuring.

The next section introduces **Communication Patterns**, exploring how these services interact through synchronous requests, asynchronous messaging, events, and publish–subscribe architectures.

---

**End of Part 3**

The next part will cover:

- Synchronous Communication
- Asynchronous Communication
- Request–Reply
- Publish–Subscribe
- Event Notification
- Event Carried State Transfer
- Request Aggregation
- Scatter–Gather
- Communication trade-offs
