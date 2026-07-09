
# 1. Separation of Concerns (SoC)

> **Separation of Concerns (SoC) is the practice of dividing a software system into distinct parts where each part is responsible for a single concern or responsibility.**

A **concern** is any aspect of a system that addresses a specific requirement.

Examples:

- Authentication
- Authorization
- Payment Processing
- Logging
- Notification
- Data Access
- Caching

Instead of mixing all concerns together,

we separate them.

---

# Why Separation of Concerns?

Imagine building an e-commerce application.

One developer writes.

```text
Order Processing

↓

Payment

↓

Logging

↓

Email

↓

Database

↓

Authentication

↓

Authorization

↓

Caching
```

All inside.

One class.

Eventually.

Nobody understands.

The code.

Every modification.

Breaks something.

---

Now imagine.

```text
Order Service

↓

Payment Service

↓

Notification Service

↓

Authentication Service

↓

Logging Framework

↓

Database Repository
```

Each concern.

Lives independently.

Changes become easier.

---

# Simple Real-World Analogy

A hospital has separate departments.

```text
Reception

↓

Doctor

↓

Laboratory

↓

Pharmacy

↓

Billing
```

Each department focuses on its responsibility.

The doctor does not perform billing.

The pharmacist does not diagnose diseases.

The hospital becomes easier to operate.

Software follows the same principle.

---

# Benefits

Separating concerns improves:

- Readability
- Testability
- Scalability
- Reusability
- Debugging
- Team ownership
- Deployment flexibility

---

# Different Levels of SoC

Separation of concerns exists at multiple levels.

```text
Enterprise

↓

Application

↓

Microservice

↓

Module

↓

Package

↓

Class

↓

Method
```

Architects apply SoC at every level.

---

# Example — UI Layer

Bad.

```text
UI

↓

Business Logic

↓

SQL Queries

↓

Logging
```

Everything mixed.

---

Good.

```text
UI

↓

Service Layer

↓

Repository

↓

Database
```

Responsibilities are separated.

---

# Example — Microservices

Instead of one service.

```text
E-Commerce Service

↓

Orders

Payments

Inventory

Shipping

Coupons

Reviews
```

Separate them.

```text
Order Service

Payment Service

Inventory Service

Shipping Service

Coupon Service

Review Service
```

Each service owns one business capability.

---

# Common Enterprise Concerns

Examples.

```text
Authentication

Authorization

Caching

Logging

Metrics

Tracing

Configuration

Persistence

Business Rules

Messaging
```

These should rarely be mixed together.

---

# Cross-Cutting Concerns

Some concerns apply to many parts of the application.

Examples.

```text
Logging

↓

Security

↓

Monitoring

↓

Tracing

↓

Auditing

↓

Caching
```

These are called **cross-cutting concerns**.

Modern frameworks often implement them using:

- Middleware
- Filters
- Interceptors
- Aspects (AOP)

---

# Example

Without separation.

```java
processOrder(){

 authenticate();

 authorize();

 log();

 validate();

 save();

 publishKafka();

 sendEmail();

 audit();

}
```

Eight different concerns.

Inside one method.

---

Better.

```text
Authentication

↓

Order Service

↓

Repository

↓

Event Publisher

↓

Notification Service
```

Cleaner.

More maintainable.

---

# Symptoms of Poor SoC

- Large classes
- Large methods
- Repeated logic
- Difficult testing
- Frequent merge conflicts
- Tight coupling
- Slow releases

These are maintainability warnings.

---

# Architect's Perspective

Architects separate concerns because business requirements evolve independently.

For example.

Authentication may change.

Without affecting payment logic.

Caching may change.

Without changing business rules.

Good architectures isolate these concerns,

allowing independent evolution.

---

# 2. Single Responsibility Principle (SRP)

> **A module, class, component, or service should have one responsibility and therefore only one reason to change.**

SRP is the first principle of SOLID.

It applies far beyond classes.

Architects apply SRP to:

- Microservices
- Modules
- APIs
- Components
- Teams

---

# What Does "One Reason to Change" Mean?

Consider.

Invoice Service.

It should change.

Only when.

Invoice requirements change.

Not because.

Authentication changed.

Or.

Email changed.

---

# Bad Example

```text
UserService

↓

Login

↓

Registration

↓

Email

↓

SMS

↓

Payment

↓

Audit

↓

Reports
```

Too many responsibilities.

---

# Better

```text
Authentication Service

↓

User Profile Service

↓

Notification Service

↓

Reporting Service
```

Each has one responsibility.

---

# Class-Level Example

Bad.

```text
Employee

↓

Salary

↓

Email

↓

PDF

↓

Database

↓

Logging
```

One class.

Doing everything.

---

Good.

```text
Employee

↓

SalaryCalculator

↓

EmailService

↓

PdfGenerator

↓

EmployeeRepository
```

Each class.

Has one purpose.

---

# Service-Level SRP

Bad.

```text
Order Service

↓

Order

↓

Inventory

↓

Shipping

↓

Payments

↓

Coupons

↓

Invoices
```

Too many responsibilities.

---

Better.

```text
Order

Inventory

Payment

Shipping

Invoice
```

Separate services.

Independent deployment.

---

# Team-Level SRP

Large organizations.

Also apply SRP.

To teams.

Example.

```text
Payments Team

↓

Payments

----------------

Inventory Team

↓

Inventory

----------------

Platform Team

↓

Infrastructure
```

Each team owns one domain.

---

# Benefits

SRP improves.

- Readability
- Testing
- Ownership
- Deployment
- Scalability
- Refactoring

---

# Common Misunderstanding

SRP does **not** mean.

One method.

Per class.

It means.

One cohesive responsibility.

---

# Signs SRP Is Violated

A class or service changes because of:

- UI changes
- Database changes
- Business rule changes
- Reporting changes
- Security changes

Multiple reasons.

Indicate.

Multiple responsibilities.

---

# Architect's Perspective

SRP reduces the **blast radius of change**.

When a responsibility changes,

only one component should require modification.

Architects should ensure that business capabilities, services, modules, and teams align around cohesive responsibilities rather than arbitrary technical boundaries.

---

# 3. High Cohesion

> **Cohesion measures how closely related the responsibilities within a module, class, or service are.**

High cohesion means.

Everything inside a component.

Belongs together.

---

# Example

Payment Service.

Contains.

```text
Authorize Payment

Capture Payment

Refund

Payment Validation

Payment History
```

All responsibilities.

Relate to payments.

High cohesion.

---

# Poor Cohesion

Payment Service.

Contains.

```text
Payments

Emails

Coupons

Authentication

Reports

Logging
```

Unrelated responsibilities.

Poor cohesion.

---

# Why High Cohesion Matters

High cohesion leads to:

- Easier understanding
- Easier testing
- Easier maintenance
- Better reuse
- Simpler deployments

---

# Detecting Low Cohesion

Questions.

Does everything belong together?

Would removing one feature improve clarity?

Do unrelated teams modify this component?

If yes.

Cohesion is probably low.

---

# Service Cohesion

Good.

```text
Inventory Service

↓

Stock

Reservation

Availability

Replenishment
```

Everything concerns inventory.

---

Bad.

```text
Inventory Service

↓

Inventory

↓

Payments

↓

Reports

↓

Authentication
```

Multiple business domains.

---

# Cohesion Across Architecture

Cohesion should exist at every level.

```text
Method

↓

Class

↓

Package

↓

Module

↓

Microservice

↓

Business Domain
```

---

# Architect's Perspective

High cohesion naturally emerges when architects model software around business capabilities rather than technical convenience.

This is one of the key ideas behind Domain-Driven Design.

---

# 4. Low Coupling

> **Coupling measures the degree of dependency between two software components.**

Low coupling means.

Components interact.

With minimal knowledge.

About each other.

---

# Why Coupling Matters

Imagine.

Order Service.

Directly depends on.

```text
Payment

Inventory

Shipping

Email

SMS

Reports

Coupons
```

Every change.

Requires coordination.

Releases slow down.

---

Better.

```text
Order

↓

Event

↓

Interested Services
```

Loose coupling.

Improves flexibility.

---

# Types of Coupling

Examples.

```text
Content Coupling

Common Coupling

Control Coupling

Data Coupling

Message Coupling
```

Architects generally aim for:

- Data coupling
- Message coupling

And avoid tighter forms.

---

# Tight Coupling Example

```text
Order Service

↓

Direct Database Access

↓

Inventory Database
```

Order Service.

Depends on Inventory schema.

Schema changes.

Break Order Service.

---

# Loose Coupling Example

```text
Order Service

↓

REST API

↓

Inventory Service
```

Or.

```text
Order Service

↓

Kafka Event

↓

Inventory Service
```

Interfaces remain stable.

Implementations can evolve independently.

---

# Benefits

Low coupling enables:

- Independent deployment
- Easier testing
- Better scalability
- Technology evolution
- Team autonomy
- Reduced regression risk

---

# Coupling vs Cohesion

Architects strive for:

```text
High Cohesion

+

Low Coupling
```

This combination creates systems that are both understandable and adaptable.

---

# Example

```text
Order Service

↓

Order Logic Only

----------------

Payment Service

↓

Payment Logic Only

----------------

Communication

↓

API / Events
```

Each service is cohesive.

Communication is loosely coupled.

---

# Common Mistakes

### Sharing Databases

Multiple services writing to the same tables creates tight coupling.

---

### Calling Internal Classes

Depend on public contracts.

Not implementation details.

---

### Circular Dependencies

```text
A

↓

B

↓

C

↓

A
```

Circular dependencies make change difficult.

---

### Excessive Synchronous Calls

Long chains of synchronous dependencies increase operational coupling and reduce resilience.

Use asynchronous communication where appropriate.

---

# Architect's Perspective

Maintainable architectures are built on two complementary principles:

- **High Cohesion** — things that belong together stay together.
- **Low Coupling** — things that change independently remain independent.

These principles reduce the cost of change, enable parallel development, and allow systems to evolve gracefully as business requirements grow.

---

**End of Part 2A**

The next part will cover:

- **DRY (Don't Repeat Yourself)**
- **KISS (Keep It Simple, Stupid)**
- **YAGNI (You Aren't Gonna Need It)**
- **Composition over Inheritance**
- **Dependency Inversion Principle**
- **SOLID from an Architectural Perspective**
