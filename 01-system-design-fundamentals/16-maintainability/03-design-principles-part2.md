
# 5. DRY (Don't Repeat Yourself)

> **DRY means every piece of knowledge, business rule, or logic should exist in exactly one authoritative place within the system.**

Notice.

DRY is about **knowledge**.

Not merely duplicated code.

The same business rule should never be implemented in multiple places.

---

# Why DRY Matters

Imagine.

A banking application.

Interest calculation.

Exists.

Here.

```text
Loan Service
```

Again.

```text
Savings Service
```

Again.

```text
Reporting Service
```

One day.

Business changes.

```
Interest Rate

10%

↓

12%
```

Developers.

Must update.

Three locations.

Miss one.

System becomes inconsistent.

---

# Good Design

```text
Interest Calculator

↓

Loan Service

↓

Savings Service

↓

Reports
```

Business rule.

Lives.

In one place.

---

# Code Duplication vs Knowledge Duplication

Many developers misunderstand DRY.

These are different.

### Code Duplication

Two methods.

Contain similar code.

Sometimes acceptable.

---

### Knowledge Duplication

Business rule.

Repeated.

Multiple places.

Dangerous.

---

# Example

Bad.

```text
Tax Calculation

↓

Checkout

↓

Invoice

↓

Reports
```

Three implementations.

---

Better.

```text
Tax Service

↓

Checkout

↓

Invoice

↓

Reports
```

One implementation.

---

# Enterprise Example

Discount Rules.

```text
Coupon

↓

Order

↓

Invoice

↓

Analytics
```

All should reuse.

Same discount engine.

---

# Benefits

DRY reduces.

- Bugs
- Maintenance effort
- Regression risk
- Inconsistent behavior

---

# Overusing DRY

Developers sometimes create.

One giant utility.

Containing unrelated logic.

This reduces readability.

Balance.

Is important.

---

# Rule of Three

Many experienced engineers follow.

The Rule of Three.

```text
Duplicate Once

↓

Accept

Duplicate Twice

↓

Observe

Duplicate Third Time

↓

Refactor
```

Avoid premature abstraction.

---

# Common Mistakes

### Premature Abstraction

Two similar methods.

Do not always require.

Immediate refactoring.

Wait.

Until patterns emerge.

---

### Shared Utility Class

```text
CommonUtils

↓

500 Methods
```

Becomes.

A God Object.

---

### Copy-Paste Business Rules

Especially dangerous.

For.

- Taxes
- Pricing
- Discounts
- Compliance
- Security

---

# Architect's Perspective

Architects apply DRY to:

- Business rules
- APIs
- Configurations
- Infrastructure
- Documentation

Not only.

Application code.

---

# 6. KISS (Keep It Simple, Stupid)

> **KISS states that systems should be designed as simply as possible while still solving the business problem.**

Complexity.

Is expensive.

Simple systems are:

- Easier to understand
- Easier to test
- Easier to maintain
- Easier to scale

---

# Why Simplicity Wins

Imagine.

Requirement.

```
Store Customer Details
```

Solution A.

```text
Microservices

↓

Kafka

↓

CQRS

↓

Event Sourcing

↓

Redis

↓

ElasticSearch
```

---

Solution B.

```text
REST API

↓

Service

↓

Database
```

If business.

Needs only CRUD.

Solution B.

Is superior.

---

# Complexity Has Cost

Every additional technology introduces.

- Learning curve
- Operational cost
- Security patches
- Monitoring
- Debugging
- Upgrades

Complexity must be justified.

---

# Simple Architecture

```text
Client

↓

API

↓

Service

↓

Database
```

Easy to understand.

Easy to maintain.

---

# Complex Architecture

```text
Gateway

↓

Service Mesh

↓

CQRS

↓

Kafka

↓

Saga

↓

Event Store

↓

Cache

↓

Search

↓

Analytics
```

Excellent.

Only when business requires it.

---

# KISS Questions

Architects should ask.

- Is there a simpler solution?
- Is this technology necessary?
- Can we remove components?
- Will another team understand this?

---

# Enterprise Example

Startup.

100 users.

Choosing Kubernetes.

Multiple service meshes.

Distributed databases.

Unnecessary complexity.

---

Large enterprise.

Millions of users.

Same technologies.

May become appropriate.

Architecture should match scale.

---

# Benefits

KISS improves.

- Maintainability
- Reliability
- Delivery speed
- Knowledge transfer
- Operational simplicity

---

# Common Mistakes

### Engineering for Resume

Choosing technologies.

Because they look impressive.

Not because business needs them.

---

### Premature Distribution

Splitting.

Ten microservices.

For a tiny application.

Adds unnecessary operational complexity.

---

### Over-Optimizing

Building.

Highly scalable architecture.

Before validating product-market fit.

---

# Architect's Perspective

Simple.

Does not mean.

Primitive.

It means.

No unnecessary complexity.

The simplest architecture that satisfies business requirements is often the most maintainable.

---

# 7. YAGNI (You Aren't Gonna Need It)

> **YAGNI means do not build functionality until there is a real business requirement for it.**

Many systems become difficult to maintain.

Because engineers build.

Features.

Nobody needs.

---

# Example

Requirement.

```
Customers can place orders.
```

Engineer designs.

```text
Coupons

↓

Gift Cards

↓

Rewards

↓

Subscriptions

↓

Affiliate Program

↓

Loyalty Points
```

Business requested.

Only.

Orders.

Everything else.

Adds maintenance burden.

---

# Why YAGNI Matters

Unused code still requires.

- Testing
- Documentation
- Security patches
- Refactoring
- Upgrades

Unused features.

Still have cost.

---

# Good Design

Build.

```text
Orders
```

Later.

If business requests.

```text
Coupons
```

Add coupons.

When needed.

---

# Benefits

YAGNI reduces.

- Complexity
- Technical debt
- Delivery time
- Maintenance effort

---

# Common Misunderstanding

YAGNI.

Does **not** mean.

Ignore future scalability.

Architectures should remain extensible.

Without implementing unnecessary features.

---

# Example

Good.

```text
Payment Interface

↓

Current Provider
```

Future providers.

Can be added.

Later.

---

Bad.

Implement.

Ten payment providers.

Before first customer.

---

# Architect's Perspective

Architects design systems.

To accommodate future change.

Without implementing every possible future feature.

Design for extension.

Not speculation.

---

# 8. Composition over Inheritance

> **Prefer building software by combining small, reusable components rather than creating deep inheritance hierarchies.**

Inheritance.

Creates strong coupling.

Composition.

Creates flexibility.

---

# Inheritance Example

```text
Vehicle

↓

Car

↓

ElectricCar

↓

AutonomousElectricCar
```

Hierarchy grows.

Rapidly.

Changes become difficult.

---

# Composition Example

```text
Vehicle

↓

Engine

↓

Battery

↓

GPS

↓

Navigation

↓

Autonomous Module
```

Features.

Can be added.

Or removed.

Independently.

---

# Enterprise Example

Notification.

Instead of.

```text
EmailNotification

↓

SmsNotification

↓

PushNotification
```

Compose.

```text
Notification

↓

Email Sender

↓

SMS Sender

↓

Push Sender
```

Easier.

To extend.

---

# Benefits

Composition improves.

- Flexibility
- Reuse
- Testing
- Maintainability

---

# Architect's Perspective

Microservices themselves represent composition.

Small business capabilities.

Combined.

To create larger systems.

Composition aligns naturally.

With modern architecture.

---

# 9. Dependency Inversion Principle (DIP)

> **High-level modules should depend on abstractions, not on concrete implementations.**

Architectures should depend on.

Contracts.

Rather than implementations.

---

# Bad Design

```text
Order Service

↓

Stripe SDK
```

Changing provider.

Requires.

Order Service changes.

---

# Better Design

```text
Order Service

↓

Payment Interface

↓

Stripe

↓

PayPal

↓

Bank
```

Order Service.

Depends only.

On abstraction.

---

# Benefits

DIP enables.

- Easier testing
- Easier replacement
- Vendor independence
- Better modularity

---

# Enterprise Example

Storage.

```text
Application

↓

Storage Interface

↓

AWS S3

↓

Azure Blob

↓

Google Cloud Storage
```

Cloud migration.

Becomes easier.

---

# Dependency Injection

Many frameworks implement DIP.

Using Dependency Injection.

```text
Application

↓

Interface

↓

Injected Implementation
```

Loose coupling.

Without manual wiring.

---

# SOLID from an Architectural Perspective

SOLID principles apply beyond classes.

| Principle | Architectural Interpretation |
|-----------|-------------------------------|
| SRP | Services own one business capability |
| OCP | Systems are extensible without modifying stable components |
| LSP | Implementations remain interchangeable through contracts |
| ISP | APIs expose only what consumers need |
| DIP | Depend on abstractions and stable interfaces |

Architects use SOLID.

To shape systems.

Not just code.

---

# Design Principles Together

These principles reinforce one another.

```text
Separation of Concerns

↓

Single Responsibility

↓

High Cohesion

↓

Low Coupling

↓

DRY

↓

KISS

↓

YAGNI

↓

Composition

↓

Dependency Inversion

↓

Highly Maintainable Architecture
```

---

# Common Mistakes

### Blindly Applying Every Principle

Design principles are guidelines.

Not rigid rules.

Use engineering judgment.

---

### Excessive Abstraction

Too many interfaces.

Too many layers.

Can increase complexity.

---

### Ignoring Business Context

The best technical design.

May still be wrong.

If it complicates simple business requirements.

---

### Framework-Driven Design

Do not let frameworks dictate architecture.

Business requirements should drive architectural decisions.

---

# Architect's Perspective

Maintainable systems are rarely created by one revolutionary idea.

They emerge from the consistent application of small design principles over thousands of engineering decisions.

When evaluating an architecture, ask:

- Is each component responsible for one concern?
- Is unnecessary complexity avoided?
- Can components evolve independently?
- Are abstractions stable?
- Can future engineers understand and modify this system safely?

If the answer is consistently "yes,"

the architecture is likely to remain maintainable for many years.

---

**End of Part 2B**

The next part will cover:

- **Modular Architecture**
- **Layered Architecture**
- **Hexagonal Architecture (Ports & Adapters)**
- **Clean Architecture**
- **Domain-Driven Design (Maintainability Perspective)**
- **API Versioning**
- **Backward Compatibility**
