
# 10. Modular Architecture

> **Modular Architecture is an architectural style where a system is divided into independent, cohesive modules, each responsible for a specific business capability or technical responsibility.**

A module is a self-contained unit that:

- Has a well-defined responsibility
- Hides its internal implementation
- Exposes only necessary interfaces
- Can evolve independently

Think of modules as **building blocks**.

A building is constructed from bricks.

A software system is constructed from modules.

---

# Why Modular Architecture?

Imagine building an ERP system.

Without modules.

```text
ERP Application

↓

Finance

HR

Payroll

Inventory

Sales

CRM

Reports

Authentication
```

Everything is mixed.

Changing payroll.

May accidentally affect inventory.

---

With modules.

```text
ERP

├── Finance
├── HR
├── Payroll
├── Inventory
├── Sales
├── CRM
└── Reporting
```

Each module evolves independently.

---

# Real-World Analogy

A shopping mall.

Contains.

```text
Food Court

↓

Clothing

↓

Electronics

↓

Cinema

↓

Parking
```

Each section has its own responsibility.

Changing one section.

Does not require rebuilding the entire mall.

Software modules work the same way.

---

# Characteristics of a Good Module

A good module should have:

- High cohesion
- Low coupling
- Clear ownership
- Stable interfaces
- Hidden implementation
- Independent testing

---

# Module Responsibilities

Each module should answer one business question.

Examples.

```text
Order Module

↓

Everything related to Orders

----------------------------

Payment Module

↓

Everything related to Payments

----------------------------

Inventory Module

↓

Everything related to Inventory
```

Avoid mixing unrelated concerns.

---

# Internal Structure

A module may contain.

```text
Controllers

↓

Services

↓

Repositories

↓

Domain Models

↓

Configuration
```

Everything related to the module.

Lives together.

---

# Communication Between Modules

Modules should communicate.

Only through.

Stable interfaces.

Example.

```text
Order Module

↓

Payment Interface

↓

Payment Module
```

Never bypass module boundaries.

---

# Benefits

Modular architecture improves:

- Maintainability
- Parallel development
- Reusability
- Testing
- Deployment flexibility
- Team ownership

---

# Monolith Can Be Modular

A common misconception.

Modularity.

Requires microservices.

False.

Even a monolith should be modular.

Example.

```text
Monolith

├── Orders
├── Payments
├── Inventory
├── Users
└── Reports
```

One deployment.

Multiple modules.

This is often called a **Modular Monolith**.

---

# Modular Monolith vs Microservices

| Modular Monolith | Microservices |
|------------------|---------------|
| Single Deployment | Multiple Deployments |
| Shared Process | Distributed Processes |
| Easier Operations | Greater Scalability |
| Lower Complexity | Higher Operational Overhead |

Many successful systems begin as modular monoliths.

---

# Module Dependency Rules

Good.

```text
Orders

↓

Payment Interface
```

Bad.

```text
Orders

↓

Payments

↓

Inventory

↓

Orders
```

Circular dependencies reduce maintainability.

---

# Module Ownership

Large organizations often assign one team per module.

```text
Payments Team

↓

Payment Module

----------------

Inventory Team

↓

Inventory Module

----------------

Commerce Team

↓

Order Module
```

Ownership becomes clear.

---

# Signs of Poor Modularization

- Circular dependencies
- Shared database tables
- Shared utility modules with unrelated code
- Frequent cross-module changes
- Large modules containing multiple domains

---

# Architect's Perspective

Architects should design modules around **business capabilities**, not technical layers.

Business capabilities change more slowly than frameworks.

This creates long-term stability.

---

# 11. Layered Architecture

> **Layered Architecture organizes software into layers where each layer has a specific responsibility and depends only on lower layers.**

It is one of the oldest.

And most widely used.

Architectural patterns.

---

# Typical Layers

```text
Presentation

↓

Application

↓

Domain

↓

Persistence

↓

Database
```

Each layer.

Has one responsibility.

---

# Responsibilities

## Presentation Layer

Responsible for:

- REST APIs
- UI
- Validation
- Request Mapping

No business rules.

---

## Application Layer

Responsible for:

- Use Cases
- Transactions
- Workflow
- Coordination

Does not contain persistence details.

---

## Domain Layer

Contains.

Business rules.

Examples.

```text
Order

Payment

Invoice

Pricing

Discount Rules
```

This layer represents the business.

---

## Persistence Layer

Responsible for.

- SQL
- NoSQL
- Repository
- ORM
- Database access

Should not contain business rules.

---

# Layer Interaction

```text
UI

↓

Application

↓

Domain

↓

Persistence

↓

Database
```

Dependencies flow downward.

Never upward.

---

# Benefits

Layering provides:

- Clear separation
- Easier testing
- Better maintainability
- Technology isolation

---

# Common Mistake

Business rules inside.

Controllers.

Example.

```text
Controller

↓

Validation

↓

Discount

↓

Database

↓

Email
```

Controller becomes bloated.

Business logic belongs in the domain or application layer.

---

# Layer Violations

Bad.

```text
UI

↓

Database
```

Presentation layer.

Should never access the database directly.

---

# Architect's Perspective

Layered architecture is simple and effective.

However, in large systems, rigid layers may become tightly coupled.

Modern architectures often evolve toward Hexagonal or Clean Architecture while preserving the core idea of separation.

---

# 12. Hexagonal Architecture (Ports & Adapters)

> **Hexagonal Architecture isolates business logic from external technologies by communicating through ports (interfaces) and adapters (implementations).**

Also known as:

- Ports & Adapters Architecture

The primary goal is to ensure that the business logic remains independent of:

- Databases
- Frameworks
- Messaging systems
- External APIs
- UI technologies

---

# Traditional Architecture

```text
Business Logic

↓

Spring

↓

JPA

↓

Database
```

Business becomes tightly coupled to frameworks.

---

# Hexagonal Architecture

```text
        REST API

           │

      Adapter

           │

Port ← Business → Port

           │

      Adapter

           │

      Database
```

Business logic depends only on ports.

---

# Ports

Ports define.

What the application needs.

Example.

```java
PaymentRepository

InventoryGateway

NotificationService
```

These are interfaces.

---

# Adapters

Adapters implement the ports.

Example.

```text
PaymentRepository

↓

PostgreSQL Adapter

--------------------

NotificationService

↓

Kafka Adapter

--------------------

Storage

↓

S3 Adapter
```

---

# Benefits

Hexagonal Architecture enables:

- Easier testing
- Easier technology replacement
- Better maintainability
- Reduced framework coupling

---

# Cloud Migration Example

Application depends on.

```text
Storage Port
```

Current adapter.

```text
AWS S3
```

Future adapter.

```text
Azure Blob Storage
```

Business logic remains unchanged.

---

# Architect's Perspective

Ports protect the core business model from technology changes.

Adapters isolate infrastructure volatility.

---

# 13. Clean Architecture

> **Clean Architecture organizes software around business rules, ensuring that high-level policies are independent of frameworks, databases, and external systems.**

Popularized by Robert C. Martin.

---

# Core Idea

Dependencies always point inward.

Toward business rules.

Never outward.

Toward frameworks.

---

# Layers

```text
Frameworks

↓

Interface Adapters

↓

Use Cases

↓

Entities
```

Entities.

Remain stable.

Everything else can change.

---

# Dependency Rule

Outer layers.

Can depend on.

Inner layers.

Inner layers.

Never depend.

On outer layers.

---

# Example

Bad.

```text
Business Logic

↓

Spring Framework
```

Good.

```text
Business Logic

↓

Interface

↓

Spring Implementation
```

---

# Benefits

- Framework independence
- Easier testing
- Better maintainability
- Stable business logic

---

# Common Misunderstanding

Clean Architecture.

Does not require.

Many layers.

Its main principle is.

Dependency direction.

Not complexity.

---

# 14. Domain-Driven Design (DDD) and Maintainability

> **Domain-Driven Design structures software around the business domain rather than technical implementation details.**

DDD improves maintainability.

By aligning software with business language.

---

# Traditional Organization

```text
Controllers

Services

Repositories

Entities
```

Technical grouping.

---

# DDD Organization

```text
Orders

Payments

Inventory

Shipping
```

Business grouping.

---

# Benefits

- Better communication
- Higher cohesion
- Clear ownership
- Easier evolution

Teams think.

In business capabilities.

Not technical layers.

---

# Bounded Context

Large enterprises.

Split domains.

Into bounded contexts.

Example.

```text
Orders

Payments

Catalog

Shipping

Customer
```

Each bounded context.

Owns its data.

Its language.

Its rules.

---

# 15. API Versioning

APIs evolve.

Clients may not upgrade immediately.

Versioning enables safe evolution.

---

# Common Strategies

```text
URI Versioning

/api/v1/orders

/api/v2/orders
```

---

```text
Header Versioning

API-Version: 2
```

---

```text
Content Negotiation

Accept:

application/vnd.company.v2+json
```

---

# Why Version APIs?

Without versioning.

Changing a response.

May break.

Thousands of clients.

Versioning preserves compatibility.

---

# 16. Backward Compatibility

> **Backward Compatibility ensures that existing clients continue to work after the system evolves.**

This is a key maintainability principle.

Especially.

For public APIs.

---

# Compatible Change

Adding.

An optional field.

```json
{
  "id":1,
  "status":"SUCCESS",
  "trackingUrl":"..."
}
```

Older clients.

Ignore unknown fields.

---

# Breaking Change

Changing.

```json
{
 "amount":"100"
}
```

To.

```json
{
 "price":"100"
}
```

Existing clients.

May fail.

---

# Compatibility Guidelines

Prefer:

- Additive changes
- Optional fields
- Default values
- Deprecation periods

Avoid:

- Renaming fields
- Removing fields
- Changing data types
- Changing semantics

---

# API Lifecycle

```text
Design

↓

Release

↓

Adoption

↓

Deprecation

↓

Retirement
```

Plan the entire lifecycle.

Not just the first release.

---

# Common Mistakes

### Modules Based on Frameworks

Example.

```text
Spring Module

Hibernate Module
```

Frameworks change.

Business domains change less frequently.

Organize by business capability.

---

### Layer Bypassing

Skipping layers.

Creates hidden dependencies.

Maintain architectural boundaries.

---

### Framework-Centric Design

Business logic should survive framework replacement.

---

### Breaking API Changes

Public APIs are long-lived contracts.

Treat them carefully.

---

### Ignoring Module Boundaries

Accessing another module's internal implementation.

Creates tight coupling.

Depend on contracts instead.

---

# Architect's Perspective

Architectural maintainability is achieved by creating **stable boundaries**.

Modules encapsulate business capabilities.

Layers separate responsibilities.

Ports isolate technology.

Clean Architecture protects business rules.

DDD aligns software with business language.

Versioning preserves client trust.

When these concepts are combined, systems become easier to evolve for years without large-scale rewrites or disruptive migrations.

---

**End of Part 3**

The next part will cover:

- **Coding Standards**
- **Naming Conventions**
- **Refactoring**
- **Technical Debt**
- **Configuration Management**
- **Feature Flags**
- **Static Code Analysis**
```
