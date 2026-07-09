
# 12. Pagination

> **Pagination is the technique of dividing a large result set into smaller, manageable pages instead of returning all records in a single response.**

Without pagination,

large APIs become:

- Slow
- Memory intensive
- Expensive
- Difficult to scale

---

# Why Pagination Matters

Imagine.

Customer API contains.

```
50 Million Customers
```

Client requests.

```http
GET /customers
```

Without pagination.

Server returns.

```
50 Million Records
```

Problems:

- Huge response
- High memory usage
- Network congestion
- Client crashes
- Database overload

---

# Better Approach

Return.

Only.

Required records.

```text
Page 1

↓

20 Records
```

---

# Basic Pagination

```http
GET /customers?page=1&size=20
```

Returns.

```
Customers

1–20
```

---

Next.

```http
GET /customers?page=2&size=20
```

Returns.

```
Customers

21–40
```

---

# Types of Pagination

```text
Pagination

├── Offset Pagination
├── Cursor Pagination
├── Keyset Pagination
└── Seek Pagination
```

Each has different trade-offs.

---

# Offset Pagination

Most common.

---

Example.

```http
GET /products?offset=100&limit=20
```

Meaning.

Skip.

```
100
```

Return.

```
20
```

---

# SQL

```sql
SELECT *
FROM products
LIMIT 20 OFFSET 100;
```

---

# Advantages

- Simple
- Easy to implement
- Easy to understand

---

# Problems

Large offsets.

Become slower.

Database scans.

Many rows.

Only to skip them.

---

# Cursor Pagination

Instead of offset.

Use.

Cursor.

---

Example.

```http
GET /products?cursor=abc123
```

Cursor points.

To last record.

---

Workflow.

```text
Page1

↓

Cursor

↓

Page2

↓

Cursor

↓

Page3
```

---

# Advantages

- Better performance
- Stable results
- Suitable for infinite scrolling

---

# Disadvantages

- More complex
- Cursor generation required

---

# Keyset Pagination

Uses indexed fields.

Example.

```http
GET /orders?afterId=100
```

SQL.

```sql
SELECT *
FROM orders
WHERE id > 100
LIMIT 20;
```

---

# Advantages

- Very fast
- Index friendly
- Scales well

---

# Limitation

Cannot easily jump.

To arbitrary pages.

---

# Choosing Pagination Strategy

| Strategy | Best For |
|-----------|----------|
| Offset | Small datasets |
| Cursor | Social feeds |
| Keyset | Large databases |
| Seek | Streaming applications |

---

# Response Format

Example.

```json
{
  "content": [],
  "page": 2,
  "size": 20,
  "totalElements": 500,
  "totalPages": 25,
  "hasNext": true
}
```

---

# Should You Return Total Count?

Depends.

Large databases.

```
COUNT(*)
```

Can be expensive.

Sometimes.

Only return.

```
hasNext
```

Instead.

---

# 13. Filtering

Filtering allows clients to retrieve.

Only relevant data.

---

Example.

```http
GET /products?category=Laptop
```

---

Multiple filters.

```http
GET /products?

category=Laptop

&brand=Apple

&price<100000
```

---

# Filter Design

Prefer.

Query parameters.

Instead of.

Complex URLs.

---

Good.

```
/products?

category=phone
```

Bad.

```
/products/category/phone
```

When many filters exist.

---

# Common Filters

- Status
- Category
- Price
- Date
- Customer
- Country
- Brand

---

# SQL Example

```sql
WHERE

category='Laptop'

AND

price<100000
```

---

# Benefits

- Smaller responses
- Faster queries
- Better UX
- Lower bandwidth

---

# 14. Sorting

Sorting changes.

Result order.

---

Example.

```http
GET /products?sort=price
```

Ascending.

---

Descending.

```http
GET /products?sort=-price
```

or

```http
GET /products?sort=price,desc
```

depending on API conventions.

---

# Multiple Sorting

Example.

```http
sort=category

sort=price
```

Or.

```
sort=category,price
```

API standards vary.

---

# SQL

```sql
ORDER BY

price DESC
```

---

# Common Sort Fields

- Price
- Created Date
- Updated Date
- Name
- Rating

---

# 15. Searching

Search differs.

From filtering.

---

Filtering.

Exact criteria.

Searching.

Partial matching.

---

Example.

```http
GET /products?search=laptop
```

---

SQL.

```sql
LIKE '%laptop%'
```

---

Enterprise Search

Large systems.

Use.

- Elasticsearch
- OpenSearch
- Solr
- Vector Search

Instead.

Of SQL LIKE.

---

# Search Features

Good search supports.

- Partial words
- Typo tolerance
- Ranking
- Relevance
- Highlighting

---

# 16. Partial Responses

Sometimes.

Client needs.

Only few fields.

---

Example.

Customer.

Needs.

Only.

```
Name

Price
```

Not.

Entire object.

---

Request.

```http
GET /products?

fields=id,name,price
```

---

Response.

```json
{
  "id":100,
  "name":"Laptop",
  "price":85000
}
```

---

Benefits.

- Smaller payload
- Faster network
- Lower cost
- Better performance

---

# Trade-offs

Additional implementation complexity.

Field validation.

Documentation.

---

# 17. Idempotency in APIs

> **An API operation is idempotent if repeating the same request results in the same final state on the server.**

This is one of the most important API design principles.

---

# Why It Matters

Imagine.

Payment request.

Network timeout.

Client retries.

Without idempotency.

Customer charged.

Twice.

---

# Example

```
POST /payments
```

Timeout.

Retry.

Duplicate payment.

---

# Solution

Idempotency Key.

---

Request.

```http
POST /payments

Idempotency-Key:

PAY123ABC
```

Server stores.

```
PAY123ABC
```

---

Retry.

Same key.

Server returns.

Previous response.

No duplicate payment.

---

# Workflow

```text
Request

↓

Store Key

↓

Process

↓

Retry

↓

Same Key?

↓

Return Previous Response
```

---

# Where Used?

Critical operations.

- Payments
- Orders
- Bank transfers
- Booking
- Ticket purchase

---

# PUT vs POST

PUT.

Naturally.

Idempotent.

---

POST.

Usually.

Not.

Need.

Idempotency key.

For business safety.

---

# 18. HATEOAS

> **HATEOAS (Hypermedia As The Engine Of Application State) allows clients to discover available actions through links included in API responses.**

One of REST's official constraints.

Rarely implemented completely.

---

# Example

Response.

```json
{
  "id":100,
  "status":"PLACED",
  "_links":{
      "cancel":"/orders/100/cancel",
      "payment":"/orders/100/payment"
  }
}
```

Client discovers.

Next actions.

Without hardcoding URLs.

---

# Benefits

- Self-discoverable APIs
- Reduced client coupling
- Better evolvability

---

# Drawbacks

- Larger payloads
- More implementation effort
- Limited adoption in many enterprise REST APIs

---

# 19. REST API Best Practices

## Use Nouns

Good.

```
/orders
```

Bad.

```
/createOrder
```

---

## Use Proper HTTP Methods

Do not use.

```
POST

For everything.
```

Use HTTP semantics correctly.

---

## Version APIs

Example.

```
/v1/orders

/v2/orders
```

We'll discuss versioning in depth later.

---

## Keep APIs Consistent

Naming.

Status codes.

Error format.

Headers.

Response structures.

Should follow organization-wide standards.

---

## Design Small Resources

Avoid returning.

Huge objects.

Split resources.

When appropriate.

---

## Use Meaningful Status Codes

Avoid.

```
200

For every response.
```

---

## Support Pagination

Never return.

Millions of records.

In one request.

---

## Design for Evolution

Expect APIs.

To live.

For years.

Adding optional fields is generally safer than removing existing ones.

---

# REST Design Example

```text
GET

/orders

----------------

GET

/orders/100

----------------

POST

/orders

----------------

PUT

/orders/100

----------------

PATCH

/orders/100

----------------

DELETE

/orders/100
```

Simple.

Predictable.

---

# Common Mistakes

### Returning Entire Database Rows

Expose only business-relevant fields.

Avoid leaking internal schema.

---

### Ignoring Pagination

Large responses reduce performance and increase costs.

---

### Business Actions Hidden in Query Parameters

Bad.

```http
GET /orders?delete=true
```

Use proper HTTP methods.

---

### Using PATCH for Full Replacement

PUT replaces the resource.

PATCH modifies selected fields.

Use each according to its semantics.

---

### Ignoring Idempotency for Payments

Retries happen.

Critical operations must tolerate them safely.

---

# Architect's Perspective

A well-designed REST API is optimized for:

- Predictability
- Performance
- Evolvability
- Developer experience

Architects should think beyond individual endpoints and instead design **a consistent API ecosystem** where every service follows the same conventions.

Clients should be able to predict:

- Resource names
- HTTP methods
- Status codes
- Error responses
- Pagination
- Filtering
- Sorting

without reading extensive documentation for every endpoint.

Consistency is one of the greatest productivity multipliers in large engineering organizations.

---

**End of Part 2C**

The next part will cover:

- **GraphQL**
- **GraphQL Schema**
- **Resolvers**
- **GraphQL Federation**
- **gRPC**
- **Protocol Buffers**
- **Unary RPC**
- **Streaming RPC**
- **REST vs GraphQL vs gRPC**
- **Technology Selection Framework**
