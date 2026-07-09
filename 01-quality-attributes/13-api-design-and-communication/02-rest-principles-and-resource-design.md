
# 1. REST (Representational State Transfer)

> **REST is an architectural style for designing networked applications where resources are identified by URIs and manipulated using a uniform interface, typically HTTP.**

REST was introduced by:

**Roy Fielding**

in his Ph.D. dissertation in 2000.

REST is **not**:

- A protocol
- A framework
- A library

It is an **architectural style**.

---

# Why REST Was Created

Before REST,

distributed systems used technologies like:

- CORBA
- DCOM
- SOAP
- RMI

These were:

- Heavy
- Complex
- Platform dependent

REST introduced:

- Simplicity
- Scalability
- Uniform communication

---

# REST Philosophy

REST is built around one simple idea:

> **Everything important in a system is modeled as a resource.**

Examples:

```
Customer

Product

Order

Invoice

Shipment

Payment
```

Not:

```
createCustomer()

updateProduct()

deleteInvoice()
```

REST focuses on **resources**, not methods.

---

# REST Architecture

```text
Client

↓

HTTP Request

↓

REST API

↓

Business Logic

↓

Database

↓

HTTP Response
```

Simple.

Stateless.

Scalable.

---

# REST Constraints

REST defines six architectural constraints.

A system satisfying these constraints is considered RESTful.

```text
REST

├── Client-Server
├── Stateless
├── Cacheable
├── Uniform Interface
├── Layered System
└── Code-On-Demand (Optional)
```

Let's study each.

---

# Client-Server Constraint

Clients and servers have separate responsibilities.

```text
Client

↓

Server
```

Client handles:

- UI
- User interaction

Server handles:

- Business logic
- Database
- Security

---

# Benefits

- Independent evolution
- Better scalability
- Easier maintenance

---

# Stateless Constraint

> **Every request must contain all the information required to process it.**

Server stores no client session state between requests.

---

Example.

Good.

```http
GET /orders/100

Authorization: Bearer token
```

Everything required.

Present.

---

Bad.

```text
Request 2

Depends

On

Request 1
```

Server remembers session.

Not RESTful.

---

# Benefits

- Horizontal scaling
- Simpler recovery
- Easier load balancing

---

# Cacheable Constraint

Responses should indicate whether they can be cached.

Example.

```http
Cache-Control: max-age=300
```

Clients avoid unnecessary requests.

Improves:

- Performance
- Scalability
- Cost

---

# Uniform Interface

Perhaps the most important REST constraint.

All resources follow consistent conventions.

Example.

```
GET /customers

GET /orders

GET /products
```

Predictable.

Easy to learn.

---

# Layered System

Clients should not know:

How many systems process the request.

```text
Client

↓

Gateway

↓

Load Balancer

↓

API

↓

Database
```

Client only communicates with the first layer.

---

# Code-on-Demand (Optional)

Server may send executable code.

Example:

JavaScript.

Rarely discussed in enterprise API interviews.

Optional constraint.

---

# Benefits of REST

REST provides:

- Simplicity
- Scalability
- Loose coupling
- Cacheability
- Technology independence
- Broad ecosystem support

---

# REST Limitations

REST is not ideal for:

- Real-time communication
- Streaming
- Extremely chatty interactions
- Strongly typed service-to-service communication

Other technologies solve these better.

---

# 2. Richardson Maturity Model

> **The Richardson Maturity Model measures how RESTful an API is.**

There are four levels.

```text
Level 0

↓

Level 1

↓

Level 2

↓

Level 3
```

---

# Level 0

Single endpoint.

RPC over HTTP.

Example.

```
POST /api
```

Everything goes through one endpoint.

Not RESTful.

---

# Level 1

Resources introduced.

```
/customers

/orders

/products
```

Better organization.

Still limited.

---

# Level 2

Uses proper HTTP methods.

```
GET

POST

PUT

DELETE
```

Most enterprise REST APIs stop here.

---

# Level 3

Hypermedia.

(HATEOAS)

Responses include links.

To next actions.

Highest REST maturity.

Rarely implemented completely.

---

# Richardson Model Summary

| Level | Description |
|--------|-------------|
| 0 | One endpoint |
| 1 | Resources |
| 2 | HTTP methods |
| 3 | Hypermedia |

---

# 3. Resources

> **A resource is any business entity exposed through an API.**

Examples.

```
Customer

Product

Order

Invoice

Shipment
```

Resources represent nouns.

Not verbs.

---

# Good Resource Names

```
customers

orders

products

payments

shipments
```

---

# Bad Resource Names

```
createOrder

deleteCustomer

saveInvoice
```

REST uses HTTP methods.

Not action names.

---

# Resource Relationships

```text
Customer

↓

Orders

↓

Order Items

↓

Product
```

Resources often relate to one another.

---

# Nested Resources

Example.

```
/customers/100/orders

/orders/100/items
```

Useful.

When ownership is clear.

Avoid excessive nesting.

---

# 4. URI Design

> **A URI should uniquely identify a resource using clear, predictable, and stable naming conventions.**

Good URI design makes APIs intuitive.

---

# Good Examples

```
/customers

/customers/100

/orders

/orders/100

/products
```

---

# Bad Examples

```
/getCustomer

/createOrder

/deleteProduct

/updateInvoice
```

HTTP methods express actions.

URIs identify resources.

---

# URI Best Practices

Use:

- Nouns
- Lowercase
- Hyphens
- Plurals
- Stable identifiers

---

# Good

```
/customer-orders

/user-profiles
```

---

# Bad

```
/CustomerOrders

/customerOrders

/customer_orders
```

Consistency matters.

---

# Resource Hierarchy

Example.

```
/customers

/customers/100

/customers/100/orders

/orders/500/items
```

Readable.

Predictable.

---

# Avoid Deep Nesting

Bad.

```
/customers/10/orders/50/items/8/payments/2
```

Too deep.

Prefer flatter APIs when practical.

---

# Query Parameters

Filtering.

```
GET /products?category=laptop
```

Sorting.

```
GET /products?sort=price
```

Pagination.

```
GET /products?page=2
```

Searching.

```
GET /products?search=macbook
```

---

# 5. HTTP Methods

HTTP methods define.

Action.

On resource.

---

# GET

Retrieve.

Resource.

Example.

```
GET /customers/100
```

Safe.

Idempotent.

---

# POST

Create.

New resource.

Example.

```
POST /customers
```

Generally not idempotent.

---

# PUT

Replace.

Entire resource.

```
PUT /customers/100
```

Idempotent.

---

# PATCH

Modify.

Only selected fields.

```
PATCH /customers/100
```

Usually used for partial updates.

Idempotency depends on the patch semantics.

---

# DELETE

Remove.

Resource.

```
DELETE /customers/100
```

Idempotent.

Deleting an already deleted resource should not create additional side effects.

---

# OPTIONS

Returns supported operations.

Useful.

For browsers.

CORS.

---

# HEAD

Returns.

Headers only.

No body.

Useful for:

- Health checks
- Metadata
- File existence

---

# HTTP Method Summary

| Method | Purpose | Safe | Idempotent |
|----------|----------|------|------------|
| GET | Read | ✅ | ✅ |
| POST | Create | ❌ | ❌ |
| PUT | Replace | ❌ | ✅ |
| PATCH | Partial Update | ❌ | Depends |
| DELETE | Delete | ❌ | ✅ |
| HEAD | Metadata | ✅ | ✅ |
| OPTIONS | Capabilities | ✅ | ✅ |

---

# Safe vs Idempotent

These terms are often confused.

---

## Safe

Does not modify server state.

Examples.

```
GET

HEAD

OPTIONS
```

---

## Idempotent

Repeated execution.

Produces same final state.

Examples.

```
PUT

DELETE

GET
```

---

# Example

PUT.

```
PUT

Name=John
```

Repeated.

Ten times.

Still.

```
John
```

Idempotent.

---

POST.

```
Create Customer
```

Repeated.

Creates.

Multiple customers.

Not idempotent.

---

# Choosing the Correct Method

| Operation | Method |
|------------|--------|
| Read Customer | GET |
| Create Order | POST |
| Replace Profile | PUT |
| Update Email | PATCH |
| Delete Product | DELETE |

---

# Common Mistakes

### Verbs in URIs

Bad.

```
POST /createOrder
```

Better.

```
POST /orders
```

---

### Using POST for Everything

Some APIs expose only POST endpoints.

This loses HTTP semantics and reduces interoperability.

---

### Ignoring Idempotency

Critical update operations should be designed with idempotency where appropriate.

---

### Deeply Nested URIs

Complex URLs are difficult to maintain.

Keep resource hierarchies meaningful but shallow.

---

# Architect's Perspective

REST succeeds because of its **uniformity**.

A developer who understands one well-designed REST API should quickly understand another.

Architects should therefore optimize for:

- Predictability
- Consistency
- Resource-oriented design
- Proper HTTP semantics
- Long-term evolvability

The best REST APIs feel **boring**—because they behave exactly as developers expect.

---

**End of Part 2A**

The next part will cover:

- **HTTP Status Codes**
- **Request Structure**
- **Response Structure**
- **HTTP Headers**
- **Content Negotiation**
- **Error Handling**
- **Pagination**
- **Filtering**
- **Sorting**
- **Searching**
- **Partial Responses**
- **HATEOAS**
