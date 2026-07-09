
# 17. Normalization

> **Normalization is the process of organizing data to eliminate redundancy, reduce anomalies, and improve data integrity.**

Normalization is one of the most fundamental concepts in relational database design.

It answers one question:

> **How should we organize data so that every business fact is stored exactly once?**

---

# Why Normalization Exists

Imagine an online shopping system.

Bad table:

| Order ID | Customer Name | Customer Phone | Product | Price |
|-----------|---------------|----------------|----------|-------|
| 101 | John | 9876543210 | Laptop | 50000 |
| 102 | John | 9876543210 | Mouse | 1000 |
| 103 | John | 9876543210 | Keyboard | 2000 |

Notice:

John's phone number appears:

```
3 Times
```

If John changes his phone number,

how many rows should be updated?

```
3
```

What if:

```
300,000
```

rows exist?

Problems begin.

---

# Problems Without Normalization

Data redundancy causes:

- Duplicate data
- Storage waste
- Update anomalies
- Insert anomalies
- Delete anomalies
- Data inconsistency

---

# Example

Customer changes email.

One row updated.

Another forgotten.

Database now contains:

```text
john@gmail.com

↓

john123@gmail.com
```

Which one is correct?

Nobody knows.

---

# Goals of Normalization

Normalization aims to:

- Eliminate duplicate data
- Improve consistency
- Simplify updates
- Reduce storage waste
- Improve integrity
- Maintain correctness

---

# Evolution

```text
Unstructured Data

↓

1NF

↓

2NF

↓

3NF

↓

BCNF

↓

Higher Normal Forms
```

Most enterprise systems stop at:

```
3NF

or

BCNF
```

---

# Functional Dependency

Before learning normalization,

we need one important concept.

---

# Definition

A Functional Dependency means:

> **One attribute uniquely determines another attribute.**

Example:

```
CustomerID

↓

Customer Name
```

Given:

CustomerID.

We always know:

Customer Name.

---

# Example

Employee.

```text
EmployeeID

↓

Employee Name

↓

Department
```

Employee ID determines:

Everything about the employee.

---

# Why Functional Dependencies Matter

Normalization separates attributes based on:

Dependency.

Improper dependencies create:

Duplicate information.

---

# 18. First Normal Form (1NF)

> **Every column must contain atomic values.**

Atomic means:

Indivisible.

---

# Bad Example

| Customer | Phone Numbers |
|-----------|---------------|
| John | 98765,99887 |

One column.

Multiple values.

Not atomic.

---

# Good Example

| Customer | Phone |
|-----------|-------|
| John | 98765 |
| John | 99887 |

Now:

One value.

Per cell.

---

# Another Example

Bad:

```text
Address

↓

Street, City, Zip
```

One column.

Multiple attributes.

---

Better:

```text
Street

City

Zip
```

Separate columns.

---

# Rules of 1NF

- Atomic values
- No repeating groups
- Unique rows
- Same data type per column

---

# 19. Second Normal Form (2NF)

> **Every non-key attribute must depend on the entire primary key.**

Applies mainly to:

Composite Keys.

---

# Example

Suppose:

Primary Key:

```text
OrderID

+

ProductID
```

Table:

| Order | Product | Product Name | Customer |
|---------|----------|-------------|-----------|

Product Name depends only on:

```
ProductID
```

Not entire key.

Violation.

---

# Correct Design

Split into:

Orders

Products

Order Items

---

# Before

```text
Order Item

↓

Product Name

Repeated
```

---

# After

```text
Order Item

↓

ProductID

↓

Product Table

↓

Product Name
```

No duplication.

---

# Goal

Every non-key attribute depends on:

Entire key.

Not part of it.

---

# 20. Third Normal Form (3NF)

> **Every non-key attribute must depend only on the primary key—not on another non-key attribute.**

---

# Example

Employee Table

| EmployeeID | Employee | Department | Manager |
|-------------|-----------|------------|----------|

Suppose:

Manager depends on:

```
Department
```

Not Employee.

Violation.

---

# Better Design

Employee

```text
EmployeeID

DepartmentID
```

Department

```text
DepartmentID

Manager
```

Manager stored once.

---

# Transitive Dependency

Example:

```
EmployeeID

↓

Department

↓

Manager
```

Manager depends indirectly.

Not directly.

Violation of:

3NF.

---

# Benefits

- Reduced duplication
- Better consistency
- Easier updates

---

# 21. Boyce-Codd Normal Form (BCNF)

BCNF strengthens:

3NF.

---

# Rule

Every determinant must be:

A candidate key.

---

# Why BCNF Exists

Some special relationships satisfy:

3NF.

Still contain redundancy.

BCNF eliminates those edge cases.

---

# Practical Advice

Most enterprise applications:

```
3NF

or

BCNF
```

Higher normal forms are rarely needed.

---

# Higher Normal Forms

Beyond BCNF:

- 4NF
- 5NF
- 6NF

These address specialized scenarios involving:

- Multi-valued dependencies
- Join dependencies
- Temporal databases

Rare in everyday enterprise systems.

---

# Normalization Summary

| Normal Form | Goal |
|--------------|------|
| 1NF | Atomic values |
| 2NF | Remove partial dependency |
| 3NF | Remove transitive dependency |
| BCNF | Every determinant is a candidate key |

---

# 22. Data Anomalies

Normalization prevents three major anomalies.

---

# Insert Anomaly

Suppose:

Department table merged with employees.

Cannot add:

New Department.

Until:

One employee exists.

Business limitation.

---

# Update Anomaly

Customer changes address.

Database contains:

```
100 Rows
```

Must update:

100 rows.

Miss one.

Data inconsistent.

---

# Delete Anomaly

Delete last employee.

Department disappears.

Business loses information.

---

# Summary

| Anomaly | Description |
|----------|-------------|
| Insert | Cannot insert independently |
| Update | Multiple updates required |
| Delete | Unintended data loss |

---

# 23. Denormalization

> **Denormalization intentionally introduces redundancy to improve read performance.**

Normalization optimizes:

Correctness.

Denormalization optimizes:

Performance.

---

# Why?

Highly normalized database:

```text
Customer

↓

Order

↓

Order Item

↓

Product

↓

Category
```

One screen may require:

```
10 Joins
```

Expensive.

---

# Denormalized Version

Order table stores:

```
Customer Name

Product Name

Category
```

Already available.

Reads become:

Much faster.

---

# Example

Normalized

```text
Order

↓

Customer

↓

Address

↓

Country
```

---

Denormalized

Order contains:

```
Customer Name

Country
```

No join.

---

# Advantages

- Faster reads
- Fewer joins
- Better reporting
- Lower query latency

---

# Disadvantages

- Duplicate data
- Larger storage
- Complex updates
- Consistency challenges

---

# Enterprise Examples

Denormalization commonly appears in:

- Analytics
- Dashboards
- Search indexes
- Reporting databases
- Data warehouses

Rarely used for transactional master data without careful consideration.

---

# Normalize vs Denormalize

| Normalization | Denormalization |
|---------------|-----------------|
| Data Integrity | Query Performance |
| Less Duplication | More Duplication |
| More Joins | Fewer Joins |
| Easier Updates | Faster Reads |

---

# 24. Domain-Driven Design (DDD) Aggregate

> **An Aggregate is a consistency boundary that groups related entities and value objects into a single unit for data modification.**

One of the most important concepts in Domain-Driven Design.

---

# Why Aggregates Exist

Business rarely updates:

One table.

It updates:

One business concept.

Example:

Order.

Order contains:

- Order
- Order Items
- Payment Status
- Shipping Address

These form:

One aggregate.

---

# Aggregate Structure

```text
Order

↓

Order Items

↓

Shipping Address

↓

Payment Status
```

Everything changes together.

---

# Aggregate Root

Every aggregate has:

One entry point.

Example:

```
Order
```

Other objects accessed through:

Order.

---

# Benefits

- Strong consistency
- Clear ownership
- Transaction boundary
- Better microservice design

---

# Aggregate Rule

External systems should reference:

Aggregate Root.

Not internal entities.

---

# Example

Good:

```
OrderID
```

Bad:

```
OrderItemID
```

Outside aggregate.

---

# Aggregate Size

Too Large

↓

Poor scalability.

---

Too Small

↓

Too many distributed transactions.

Choose boundaries based on:

Business consistency requirements.

---

# 25. ER Diagram Best Practices

Good ER diagrams should:

- Use business names
- Clearly identify ownership
- Define cardinality
- Show mandatory relationships
- Avoid unnecessary complexity

---

# Good Example

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

Easy to understand.

---

# Poor Example

```text
Table001

↓

Table002

↓

Table003
```

No business meaning.

---

# Modeling Guidelines

Always ask:

- Who owns this entity?
- Can it exist independently?
- What is the lifecycle?
- How often does it change?
- Is it master or transactional data?
- Should it belong to the same aggregate?

---

# Common Modeling Mistakes

### Giant Table

Everything stored in:

```
Customer_Order_Product
```

Hard to maintain.

---

### Missing Relationships

No foreign keys.

Business integrity lost.

---

### Premature Denormalization

Duplicate data introduced before performance problems exist.

---

### Technical Modeling

Design based on framework limitations instead of business requirements.

---

# Architect's Perspective

Normalization is **not a goal by itself**.

Business correctness is.

Experienced architects begin with:

- Well-normalized transactional models

Then,

only after measuring performance,

introduce carefully controlled denormalization where justified.

Similarly,

DDD aggregates remind us that databases store rows,

but businesses operate on **business concepts**.

Architects design around:

- Consistency boundaries
- Ownership
- Business invariants

rather than tables alone.

A well-designed data model becomes the foundation upon which APIs, microservices, analytics, and reporting are built.

---

**End of Part 3**

The next part will cover:

- **Relational Databases**
- **NoSQL Databases**
- **SQL vs NoSQL**
- **Key-Value Stores**
- **Document Databases**
- **Column-Family Databases**
- **Graph Databases**
- **Time-Series Databases**
- **Object Storage**
- **Choosing the Right Database**
