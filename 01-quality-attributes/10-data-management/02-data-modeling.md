
# 10. Data Modeling

> **A database stores data, but a data model represents the business.**

Poor data models create:

- Complex code
- Poor performance
- Difficult scaling
- Duplicate data
- Incorrect business rules

Good data models survive for decades.

Many enterprise systems evolve,

but their core data model remains stable.

---

# What Is Data Modeling?

Data Modeling is:

> **The process of identifying business entities, their attributes, relationships, constraints, and lifecycle before implementing a database.**

Notice:

Data modeling is about:

```
Business

↓

Then

↓

Database
```

Not the other way around.

---

# Why Data Modeling Matters

Imagine designing an e-commerce platform.

Without proper modeling,

developers create:

```text
Customer Table

↓

Order Table

↓

Product Table

↓

Random Relationships
```

Soon:

- Duplicate customers
- Missing addresses
- Invalid orders
- Difficult reporting

Business suffers.

---

# Building a House Analogy

Nobody starts building a skyscraper by pouring concrete randomly.

First:

```text
Requirements

↓

Blueprint

↓

Construction
```

Likewise:

```text
Business Requirements

↓

Data Model

↓

Database
```

---

# Data Modeling Goals

A good data model should provide:

- Business correctness
- Minimal redundancy
- Data integrity
- Easy maintenance
- Scalability
- Performance
- Flexibility

---

# Three Levels of Data Modeling

Enterprise systems usually design data at three levels.

```text
Conceptual

↓

Logical

↓

Physical
```

Each serves a different purpose.

---

# 11. Conceptual Data Model

> **The Conceptual Data Model describes the business without considering databases or technology.**

Audience:

- Business Analysts
- Domain Experts
- Architects
- Product Owners

---

# Focus

Questions answered:

- What business entities exist?
- How are they related?
- What business rules exist?

Ignore:

- Tables
- Columns
- Data Types
- Indexes

---

# Example

Online Shopping.

```text
Customer

↓

Places

↓

Order

↓

Contains

↓

Product
```

Business understands this.

No SQL involved.

---

# Characteristics

- High-level
- Technology independent
- Easy to understand
- Business language

---

# Example

Hospital.

```text
Doctor

↓

Treats

↓

Patient

↓

Receives

↓

Prescription
```

No discussion about:

VARCHAR.

INTEGER.

Indexes.

---

# Benefits

- Shared understanding
- Business validation
- Requirement discovery
- Better communication

---

# Deliverables

Typically includes:

- Entities
- Relationships
- Business Rules

---

# 12. Logical Data Model

> **The Logical Data Model adds attributes, identifiers, and relationships while remaining independent of a specific database technology.**

Audience:

- Architects
- Database Designers
- Developers

---

# Focus

Defines:

- Entities
- Attributes
- Primary Keys
- Foreign Keys
- Cardinality
- Constraints

Still ignores:

- Storage engine
- Index types
- Partitioning

---

# Example

Customer

```text
Customer

CustomerID

FirstName

LastName

Email

Phone
```

Order

```text
Order

OrderID

OrderDate

Status

CustomerID
```

Relationship:

```
Customer

1

↓

Many

Orders
```

---

# Business Rules

Examples:

- One customer can place many orders.
- One order belongs to one customer.
- One product may appear in many orders.

---

# Cardinality

Very important concept.

Represents:

How many records relate.

---

# One-to-One

Example:

```text
Employee

↓

Passport
```

One employee.

One passport.

---

# One-to-Many

Example:

```text
Customer

↓

Orders
```

One customer.

Many orders.

Most common relationship.

---

# Many-to-Many

Example:

```text
Students

↓

Courses
```

One student:

Many courses.

One course:

Many students.

Requires:

Junction table.

---

# Logical Model Example

```text
Customer

↓

Order

↓

Order Item

↓

Product
```

Much closer to implementation.

Still technology independent.

---

# 13. Physical Data Model

> **The Physical Data Model defines exactly how data will be stored inside a specific database system.**

Audience:

- Database Administrators
- Backend Engineers
- Performance Engineers

---

# Focus

Defines:

- Tables
- Columns
- Data Types
- Indexes
- Partitioning
- Storage
- Constraints

Database-specific.

---

# Example

Customer Table

```sql
Customer

customer_id BIGINT

first_name VARCHAR(100)

email VARCHAR(255)

created_at TIMESTAMP
```

Now implementation begins.

---

# Physical Design Includes

- Data Types
- Column Length
- Primary Keys
- Indexes
- Compression
- Partition Keys
- Tablespaces

---

# Example

Logical:

```
Customer Name
```

Physical:

```sql
VARCHAR(100)
```

Logical:

```
Customer ID
```

Physical:

```sql
BIGINT
```

---

# Comparison

| Model | Focus | Audience |
|---------|-------|----------|
| Conceptual | Business | Business Teams |
| Logical | Structure | Architects |
| Physical | Implementation | Developers & DBAs |

---

# Evolution

```text
Business

↓

Conceptual

↓

Logical

↓

Physical

↓

Database
```

Never reverse this order.

---

# 14. Entities

An Entity represents:

> **A real-world business object that the organization wants to manage.**

Examples:

- Customer
- Product
- Order
- Employee
- Payment
- Invoice

---

# Entity Characteristics

Every entity:

- Has identity
- Has attributes
- Exists independently
- Participates in relationships

---

# Customer Example

```text
Customer

↓

Customer ID

Name

Email

Phone
```

Identity:

Customer ID.

---

# Strong Entity

Exists independently.

Example:

```
Customer
```

---

# Weak Entity

Depends on another entity.

Example:

```
Order Item
```

Cannot exist without:

```
Order
```

---

# 15. Attributes

Attributes describe an entity.

Customer:

```text
Customer

↓

Name

Email

Phone

DOB
```

---

# Types of Attributes

Simple

Example:

```
Age
```

---

Composite

Example:

```
Address

↓

Street

City

Zip
```

---

Derived

Example:

```
Age

Derived From

DOB
```

---

Multi-valued

Example:

```
Phone Numbers
```

Multiple values.

---

# Mandatory vs Optional

Mandatory

```
Customer ID
```

Cannot be null.

---

Optional

```
Middle Name
```

May be absent.

---

# 16. Relationships

Entities rarely exist alone.

Relationships connect them.

---

# Customer Places Orders

```text
Customer

↓

Places

↓

Order
```

---

# Order Contains Products

```text
Order

↓

Contains

↓

Product
```

---

# Product Belongs To Category

```text
Product

↓

Category
```

Relationships model business interactions.

---

# Relationship Types

One-to-One

```text
Person

↓

Passport
```

---

One-to-Many

```text
Customer

↓

Orders
```

---

Many-to-Many

```text
Order

↓

Products
```

Implemented using:

```
Order Item
```

---

# ER Diagram

Simple shopping model.

```text
Customer

1

↓

*

Order

1

↓

*

Order Item

*

↓

1

Product
```

This becomes the foundation of implementation.

---

# Business Constraints

Relationships often include rules.

Example:

```
Customer

Must Exist

Before

Order
```

Another:

```
Order

Must Have

At Least One

Order Item
```

Data models capture these constraints.

---

# Common Modeling Mistakes

## Mistake 1

Modeling database tables before understanding the business.

---

## Mistake 2

Using technical names.

Bad:

```
tbl001
```

Good:

```
Customer
```

---

## Mistake 3

Missing ownership.

Who owns:

Customer?

Order?

Inventory?

Should always be clear.

---

## Mistake 4

Ignoring future growth.

Example:

Today:

One address.

Tomorrow:

Multiple addresses.

Model should anticipate reasonable business evolution.

---

# Architect's Perspective

Junior engineers often think:

> "Data modeling is about creating tables."

Experienced architects know:

**Data modeling is about understanding the business.**

A well-designed conceptual model leads naturally to:

- Correct logical models
- Efficient physical schemas
- Cleaner APIs
- Simpler microservices
- Easier scalability

Poor data models are expensive to fix because every API, report, integration, and business workflow depends on them.

Investing time in modeling before implementation pays dividends throughout the lifetime of the system.

---

**End of Part 2**

The next part will cover:

- **Normalization**
- **Functional Dependencies**
- **1NF, 2NF, 3NF, BCNF**
- **Denormalization**
- **Aggregate Design (DDD)**
- **ER Diagram Best Practices**
- **Real Enterprise Modeling Examples**
