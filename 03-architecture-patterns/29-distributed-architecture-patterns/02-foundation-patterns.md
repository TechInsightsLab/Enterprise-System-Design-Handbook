
# 2. Foundation Patterns

Before.

Learning.

Microservices.

CQRS.

Saga.

Or Event Sourcing.

We must understand.

The foundations.

Of software architecture.

---

# Why Foundation Patterns Matter

Many engineers.

Want to build.

Microservices.

Immediately.

Without understanding.

How software.

Should be structured.

Internally.

This leads to.

Distributed monoliths.

Instead of.

Distributed systems.

---

Remember.

A poorly designed.

Monolith.

Becomes.

A poorly designed.

Microservice.

---

# Evolution of Software Architecture

Most enterprise systems.

Evolve.

Like this.

```text
Simple Application

↓

Layered Architecture

↓

Modular Monolith

↓

Hexagonal Architecture

↓

Clean Architecture

↓

Microservices

↓

Event-Driven Architecture

↓

Cloud Native Platform
```

Notice.

Each stage.

Builds.

On the previous one.

---

# Why Architects Should Learn These First

Consider.

Amazon.

Netflix.

Uber.

None of them.

Started.

With.

Thousands.

Of microservices.

They started.

With.

Simple systems.

Then.

Gradually evolved.

Architecture.

As business grew.

---

# Foundation Pattern Comparison

| Pattern | Primary Goal | Typical Scale |
|----------|--------------|---------------|
| Layered Architecture | Simplicity | Small Applications |
| Modular Monolith | Maintainability | Medium–Large Systems |
| Hexagonal Architecture | Decoupling | Enterprise Applications |
| Clean Architecture | Long-Term Maintainability | Large Enterprise Systems |

These patterns.

Are not competitors.

They represent.

Architectural evolution.

---

# 2.1 Layered Architecture

The oldest.

Most common.

Architecture pattern.

---

# Definition

> **Layered Architecture organizes an application into horizontal layers where each layer has a specific responsibility and communicates only with adjacent layers.**

---

# Business Motivation

Suppose.

An online store.

Needs.

- User Interface
- Business Logic
- Database

Separating.

These concerns.

Makes.

The application.

Easier.

To understand.

---

# Architecture

```text
Presentation Layer

↓

Business Layer

↓

Persistence Layer

↓

Database
```

Sometimes.

Additional layers.

Exist.

Such as.

```text
Presentation

↓

Application

↓

Domain

↓

Infrastructure

↓

Database
```

---

# Layer Responsibilities

## Presentation Layer

Responsible for.

- UI
- REST APIs
- Controllers
- Request Validation
- Authentication Entry Point

Should NOT contain.

Business rules.

---

## Business Layer

Contains.

Business logic.

Examples.

```text
Place Order

Calculate Discount

Transfer Money

Generate Invoice
```

This layer.

Represents.

The business.

---

## Persistence Layer

Responsible for.

Database interaction.

Examples.

- Repository
- DAO
- SQL Queries
- ORM

Should NOT contain.

Business decisions.

---

## Database Layer

Stores.

Business data.

Examples.

- PostgreSQL
- MySQL
- Oracle
- MongoDB

---

# Example

Customer.

Places an order.

```text
Browser

↓

REST Controller

↓

Order Service

↓

Order Repository

↓

Database
```

Each layer.

Has.

Exactly one.

Responsibility.

---

# Advantages

- Easy to understand
- Easy to develop
- Good separation of concerns
- Familiar to most developers
- Simple testing

---

# Disadvantages

Large systems.

Often become.

```text
Presentation

↓

Huge Service Layer

↓

Huge Repository Layer
```

Business logic.

Starts spreading.

Across layers.

Creating.

Maintenance problems.

---

# Common Anti-Pattern

The.

**God Service.**

```text
OrderService

↓

20,000 Lines

↓

Everything Happens Here
```

Very common.

In enterprise applications.

---

# When to Use

- Small applications
- Internal tools
- CRUD systems
- MVPs
- Enterprise portals

---

# When NOT to Use

- Large domains
- Complex business rules
- Highly scalable platforms

---

# 2.2 Modular Monolith

Many people think.

Monolith.

Means.

Bad architecture.

This is.

False.

---

# Definition

> **A Modular Monolith is a single deployable application composed of well-defined, independent business modules with clear boundaries.**

---

# Important

A Modular Monolith.

Is NOT.

A Big Ball of Mud.

It is.

Well structured.

Internally.

---

# Business Motivation

Imagine.

An e-commerce platform.

Contains.

```text
Orders

Inventory

Payments

Shipping

Users
```

Instead of.

One giant package.

Each becomes.

An independent module.

---

# Architecture

```text
Application

│

├── Order Module

├── Payment Module

├── Inventory Module

├── Shipping Module

└── User Module
```

Single deployment.

Multiple business modules.

---

# Module Rules

Each module.

Owns.

Its.

- Business logic
- Entities
- Services
- Repositories

Modules communicate.

Only through.

Well-defined interfaces.

---

Wrong.

```text
Order Module

↓

Direct SQL

↓

Payment Tables
```

Correct.

```text
Order Module

↓

Payment API

↓

Payment Module
```

---

# Advantages

- Simple deployment
- Easy debugging
- Strong modularity
- No network calls
- No distributed transactions

---

# Why Many Companies Start Here

Microservices.

Introduce.

Distributed complexity.

A Modular Monolith.

Allows.

Teams.

To validate.

Business boundaries.

Before.

Splitting services.

---

# Real Example

Suppose.

Later.

Inventory.

Needs.

Independent scaling.

Only.

The Inventory Module.

Can become.

A microservice.

Minimal changes.

Required.

---

# When to Use

- Growing startups
- Medium enterprises
- Products with evolving domains
- Teams learning Domain-Driven Design

---

# When NOT to Use

- Independent deployments required
- Hundreds of development teams
- Independent technology stacks needed

---

# Common Anti-Pattern

Calling.

Everything.

A module.

While.

Every module.

Directly accesses.

Every database table.

This is.

A distributed.

Big Ball of Mud.

---

# 2.3 Hexagonal Architecture (Ports & Adapters)

One of the most.

Influential.

Enterprise patterns.

---

# Why It Exists

Traditional.

Layered architecture.

Often couples.

Business logic.

To frameworks.

---

Example.

Business logic.

Depends on.

```text
Spring

Hibernate

Oracle

Kafka
```

Changing.

Any technology.

Requires.

Business changes.

---

Hexagonal Architecture.

Solves this.

---

# Definition

> **Hexagonal Architecture isolates business logic from external technologies by communicating through Ports and Adapters.**

---

# Architecture

```text
                 REST

                  │

             REST Adapter

                  │

          Input Port (Interface)

                  │

            Business Domain

                  │

         Output Port (Interface)

                  │

        Database Adapter

                  │

             PostgreSQL
```

---

# Core Principle

Business logic.

Should not know.

Whether data comes from.

- REST
- Kafka
- CLI
- Database
- Message Queue

Business logic.

Only knows.

Interfaces.

---

# Ports

A Port.

Defines.

What the business.

Needs.

Example.

```java
PaymentRepository
```

No implementation.

Only.

Contract.

---

# Adapters

Adapters.

Implement.

Ports.

Example.

```text
PostgreSQL Adapter

MongoDB Adapter

Redis Adapter
```

Business logic.

Never changes.

---

# Benefits

Suppose.

Tomorrow.

Database changes.

```text
PostgreSQL

↓

MongoDB
```

Only.

Adapter changes.

Business logic.

Remains.

Untouched.

---

# Advantages

- Highly testable
- Technology independent
- Framework independent
- Easy replacement of infrastructure
- Excellent maintainability

---

# Common Misconception

People think.

Hexagonal.

Means.

Using.

Hexagon diagrams.

False.

The hexagon.

Simply emphasizes.

That.

The application.

Can have.

Many input.

And output adapters.

---

# 2.4 Clean Architecture

Popularized by.

Robert C. Martin.

(Uncle Bob)

---

# Goal

Protect.

Business rules.

From.

Everything else.

---

# Dependency Rule

The most important.

Rule.

In Clean Architecture.

Is.

```text
Dependencies

Always Point

Inward
```

Never outward.

---

# Architecture

```text
Frameworks

↓

Interface Adapters

↓

Use Cases

↓

Entities
```

Business entities.

Must never.

Depend on.

Frameworks.

---

# Example

Wrong.

```text
Business Entity

↓

Spring Annotation

↓

Hibernate Annotation
```

Correct.

```text
Business Entity

↓

Pure Business Logic
```

Frameworks.

Depend on.

Business.

Not.

The reverse.

---

# Clean Architecture vs Hexagonal

They are.

Very similar.

| Clean Architecture | Hexagonal Architecture |
|--------------------|------------------------|
| Focus on dependency direction | Focus on ports & adapters |
| Layers organized around business rules | Interfaces isolate business logic |
| Highly testable | Highly testable |
| Technology independent | Technology independent |

Many modern systems.

Combine.

Both approaches.

---

# Comparison of Foundation Patterns

| Pattern | Best For | Strength | Weakness |
|----------|----------|----------|----------|
| Layered | Small applications | Simple | Tight coupling over time |
| Modular Monolith | Medium systems | Strong modularity | Single deployment |
| Hexagonal | Enterprise systems | Decoupling | More abstractions |
| Clean Architecture | Large domains | Long-term maintainability | Higher learning curve |

---

# Architecture Evolution Decision

```text
Small CRUD App

↓

Layered

────────────────────

Growing Product

↓

Modular Monolith

────────────────────

Complex Domain

↓

Hexagonal

────────────────────

Large Enterprise

↓

Clean Architecture

────────────────────

Need Independent Deployment

↓

Microservices
```

Notice.

Microservices.

Are.

An evolution.

Not.

A starting point.

---

# Common Misconceptions

### Misconception 1

> Monolith means bad architecture.

False.

A well-designed.

Modular Monolith.

Can outperform.

Poorly designed.

Microservices.

---

### Misconception 2

> Hexagonal Architecture requires six layers.

False.

"Hexagonal".

Is a metaphor.

Not.

A structural requirement.

---

### Misconception 3

> Clean Architecture replaces Hexagonal Architecture.

False.

They solve.

Very similar problems.

Many systems.

Use ideas.

From both.

---

### Misconception 4

> Microservices are always better than monoliths.

False.

Architecture.

Should evolve.

With business needs.

Not trends.

---

# Architect's Perspective

Foundation patterns determine **how well your application evolves before it ever becomes distributed**.

A poorly structured monolith almost always becomes a poorly structured microservice ecosystem.

Experienced architects therefore spend significant effort defining **module boundaries, dependency direction, and business ownership** long before introducing distributed systems.

Getting the foundation right dramatically reduces the cost of future decomposition into microservices.

---

**End of Part 2**

The next part will begin **Service Decomposition Patterns**, covering:

- Why Microservices Exist
- Monolith vs Microservices
- Domain-Driven Decomposition
- Bounded Context
- API Gateway
- Backend for Frontend (BFF)
- Database per Service
