
# Data Management

> **Part I – System Design Foundations**

---

# Data Management

| Attribute | Value |
|-----------|--------|
| **Version** | 2.0 |
| **Part** | Part I – System Design Foundations |
| **Chapter** | 10 |
| **Reading Time** | ~350–450 Minutes |
| **Difficulty** | Advanced |
| **Last Updated** | July 2026 |

---

> [!NOTE]
> **Data Management is the discipline of designing, storing, organizing, securing, governing, and operating data so that it remains accurate, available, scalable, secure, and valuable throughout its lifecycle.**

Every software system exists because of data.

Applications are temporary.

Servers can be replaced.

Containers come and go.

Services are redeployed daily.

**Data is the true business asset.**

Architects therefore spend more time designing data than writing APIs.

---

# Table of Contents

## Part 1 – Foundations

- Learning Objectives
- Why Data Management Matters
- Business Story
- Terminology
- Data Lifecycle
- Data Quality
- Data Ownership
- Master Data
- Metadata

## Part 2 – Data Modeling

- Conceptual Data Model
- Logical Data Model
- Physical Data Model
- Normalization
- Denormalization
- ER Diagrams
- Domain Modeling
- Aggregate Design

## Part 3 – Storage Systems

- Relational Databases
- NoSQL Databases
- SQL vs NoSQL
- Key-Value Stores
- Document Databases
- Column Family Databases
- Graph Databases
- Time-Series Databases
- Object Storage

## Part 4 – Distributed Data

- Partitioning
- Sharding
- Replication
- Read Replicas
- Multi-Region Databases
- Distributed Storage
- Data Locality

## Part 5 – Consistency & Reliability

- ACID
- BASE
- CAP Theorem
- PACELC
- Consistency Models
- Data Recovery

## Part 6 – Performance

- Indexing
- Query Optimization
- Caching
- Materialized Views
- Compression
- Archiving

## Part 7 – Governance

- Data Security
- Encryption
- Backup
- Disaster Recovery
- Retention
- Compliance
- Data Governance

## Part 8 – Enterprise

- Cloud Database Services
- Enterprise Case Studies
- Production Incidents
- Architecture Review Checklist
- ADR

## Part 9 – Interview Preparation

- Beginner Questions
- Intermediate Questions
- Senior Architect Questions
- Principal Architect Questions

## Part 10 – Revision

- Cheat Sheet
- Summary
- GitHub Structure

---

# Learning Objectives

After completing this chapter, you should be able to:

- Design enterprise-grade data architectures.
- Select appropriate database technologies.
- Model business domains correctly.
- Choose between SQL and NoSQL.
- Design partitioning and sharding strategies.
- Understand CAP and PACELC trade-offs.
- Design scalable and resilient storage systems.
- Secure enterprise data.
- Design backup and disaster recovery strategies.
- Answer Staff and Principal Architect interview questions.

---

# Prerequisites

Readers should already understand:

- High Availability
- Reliability
- Scalability
- Performance
- Fault Tolerance
- Resilience
- Consistency
- Distributed Transactions
- Concurrency

Basic knowledge of:

- Databases
- Networking
- Operating Systems
- Cloud Computing

is helpful.

---

# Previous Chapters

- High Availability
- Reliability
- Scalability
- Performance
- Fault Tolerance
- Resilience
- Consistency
- Distributed Transactions
- Concurrency

---

# Next Chapters

- Caching
- Event-Driven Architecture
- Messaging
- API Design

---

# Terminology

| Term | Definition |
|------|------------|
| Data | Raw facts representing business information |
| Information | Data processed into meaningful insights |
| Database | Organized collection of data |
| Schema | Structure defining how data is stored |
| Record | Single business entity |
| Table | Collection of related records |
| Entity | Business object represented in data |
| Index | Data structure that accelerates queries |
| Partition | Logical division of data |
| Shard | Physical distribution of data across nodes |
| Replica | Copy of data for availability or performance |
| Metadata | Data describing other data |
| Master Data | Core business entities shared across systems |

---

# Opening Business Story

Imagine building an e-commerce platform.

Customers browse products.

They place orders.

They make payments.

Products ship worldwide.

At first glance,

the application appears to be about:

- APIs
- UI
- Microservices

In reality,

the business depends on correctly managing data.

---

# Business Data

The company owns:

```text
Customers

↓

Products

↓

Orders

↓

Payments

↓

Inventory

↓

Invoices
```

Every decision depends on these records.

---

# What Happens If Data Is Wrong?

Imagine:

Inventory:

```
100 Units
```

Database corruption occurs.

Inventory becomes:

```
10 Units
```

System stops selling products.

Revenue drops.

---

Or:

Customer addresses disappear.

Orders cannot be delivered.

---

Or:

Payments duplicated.

Company refunds millions.

---

# Lesson

Business survives application failures.

Business rarely survives prolonged data failures.

---

# Why Data Management Matters

Every enterprise system answers questions like:

- Who is the customer?
- What products exist?
- Which payments succeeded?
- What inventory remains?
- What invoices were generated?
- Which employee approved this transaction?

All answers come from:

```
Data
```

---

# Definition

Data Management is:

> **The discipline of creating, storing, organizing, protecting, governing, and operating data throughout its entire lifecycle while ensuring correctness, availability, security, scalability, and business value.**

Notice:

This definition includes much more than databases.

It includes:

- Governance
- Security
- Backup
- Compliance
- Recovery
- Performance
- Operations

---

# Data Is a Business Asset

Buildings depreciate.

Servers become obsolete.

Programming languages evolve.

Business data becomes **more valuable** over time.

Example:

A retailer's purchase history spanning ten years enables:

- Demand forecasting
- Personalized recommendations
- Fraud detection
- Customer segmentation

Historical data creates competitive advantage.

---

# Characteristics of Enterprise Data

Good enterprise data should be:

- Accurate
- Complete
- Consistent
- Available
- Secure
- Scalable
- Recoverable
- Auditable
- Governed

Missing any one characteristic creates business risk.

---

# Data vs Information vs Knowledge

These terms are often confused.

---

## Data

Raw facts.

Example:

```
Product Price

₹99
```

---

## Information

Data with context.

Example:

```
Today's Sales

₹25 Lakhs
```

---

## Knowledge

Information interpreted for decision making.

Example:

```
Sales increase every weekend.

Increase inventory every Friday.
```

---

# Evolution of Data

```text
Data

↓

Information

↓

Knowledge

↓

Business Decisions

↓

Business Value
```

Good architecture accelerates this transformation.

---

# Data Lifecycle

Every piece of business data passes through a lifecycle.

```text
Create

↓

Store

↓

Process

↓

Share

↓

Archive

↓

Delete
```

Architects design each phase.

---

# Example

Customer account.

```text
Register

↓

Update Profile

↓

Place Orders

↓

Inactive

↓

Archive

↓

Delete
```

The application changes.

The lifecycle remains.

---

# Data Ownership

One of the most important architectural concepts.

Every piece of data should have:

Exactly one owner.

---

# Example

Customer Service owns:

```
Customer Profile
```

Order Service references it.

Inventory Service references it.

Ownership remains:

Customer Service.

---

# Why Ownership Matters

Without ownership:

Multiple systems update:

```
Customer Email
```

Eventually:

Different databases contain different values.

Chaos.

---

# Single Source of Truth (SSOT)

Every important business entity should have:

One authoritative source.

Example:

```text
Customer

↓

Customer Service

↓

Source of Truth
```

Other systems consume or cache,

but do not own the master record.

---

# Master Data

Master Data represents core business entities.

Examples:

- Customers
- Products
- Suppliers
- Employees
- Warehouses
- Branches

Master Data changes relatively slowly,

but is referenced everywhere.

---

# Transactional Data

Unlike master data,

transactional data changes continuously.

Examples:

- Orders
- Payments
- Deliveries
- Refunds
- Shipments

Volume grows rapidly.

---

# Reference Data

Reference Data defines standard values.

Examples:

- Countries
- Currencies
- Tax Codes
- Languages
- Status Codes

Usually read-only.

Shared across systems.

---

# Metadata

Metadata means:

> **Data about data.**

Example:

Customer table.

Metadata describes:

- Columns
- Data Types
- Constraints
- Owners
- Retention Policies

Not customer records themselves.

---

# Data Classification

Enterprise organizations classify data.

Example:

```text
Public

↓

Internal

↓

Confidential

↓

Restricted
```

Security policies depend on classification.

---

# Examples

Public:

Product catalog.

---

Internal:

Employee directory.

---

Confidential:

Customer information.

---

Restricted:

Passwords.

Encryption keys.

Financial records.

---

# Data Quality

Poor data quality causes:

- Incorrect reports
- Wrong business decisions
- Duplicate customers
- Failed analytics
- Customer dissatisfaction

---

# Dimensions of Data Quality

| Dimension | Meaning |
|-----------|----------|
| Accuracy | Correct values |
| Completeness | No missing information |
| Consistency | Same value everywhere |
| Timeliness | Up-to-date |
| Validity | Follows rules |
| Uniqueness | No duplicates |

---

# Example

Customer appears twice.

```text
John Smith

----------------

J. Smith
```

Business thinks:

Two customers.

Reality:

One customer.

Poor data quality.

---

# Data Management Layers

Enterprise systems manage data across multiple layers.

```text
Business

↓

Applications

↓

APIs

↓

Database

↓

Storage

↓

Backup

↓

Disaster Recovery

↓

Compliance
```

Architects design all layers,

not just the database.

---

# Common Systems That Manage Data

Examples:

- Banking
- Healthcare
- Retail
- Logistics
- Government
- Insurance
- Telecommunications
- Manufacturing

Every industry depends on trustworthy data.

---

# Architect's Perspective

Junior engineers often view databases as a persistence mechanism.

Experienced architects view **data as the product**.

Applications are merely one way to interact with that product.

Every architectural decision should begin with questions such as:

- Who owns this data?
- How long should it live?
- How valuable is it?
- Who may access it?
- How will it scale?
- How will it be recovered?
- How will it evolve over time?

Answering these questions correctly is the foundation of enterprise architecture.

---

**End of Part 1**

The next part will cover:

- **Conceptual Data Modeling**
- **Logical Data Modeling**
- **Physical Data Modeling**
- **Entities**
- **Relationships**
- **Normalization**
- **Denormalization**
- **Domain-Driven Design Aggregates**
- **ER Diagrams**
