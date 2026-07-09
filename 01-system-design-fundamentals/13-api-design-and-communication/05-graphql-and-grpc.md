
# 20. GraphQL

> **GraphQL is a query language and runtime for APIs that allows clients to request exactly the data they need—no more and no less.**

GraphQL was developed by:

**Facebook (Meta)**

to solve limitations of traditional REST APIs.

Unlike REST,

GraphQL exposes:

A **single endpoint**

while allowing flexible queries.

---

# Why GraphQL Was Created

Imagine a mobile application displaying a product page.

The UI needs:

- Product Name
- Price
- Rating
- Seller Name
- Stock

With REST,

the client may call multiple APIs.

```text
GET /products/100

↓

GET /ratings/100

↓

GET /inventory/100

↓

GET /seller/10
```

Multiple requests.

More latency.

---

# GraphQL Solution

One request.

```graphql
query {

  product(id:100){

      name

      price

      rating

      seller{

          name

      }

      inventory{

          available

      }

  }

}
```

Single response.

Exactly requested fields.

---

# GraphQL Architecture

```text
Client

↓

GraphQL API

↓

Resolvers

↓

Microservices

↓

Database
```

---

# Key Characteristics

- Single endpoint
- Flexible queries
- Strong typing
- Client-controlled response
- Schema-driven

---

# GraphQL Terminology

```text
GraphQL

├── Schema
├── Query
├── Mutation
├── Subscription
├── Resolver
└── Type System
```

---

# GraphQL Schema

> **The schema defines every object, field, query, mutation, and relationship available in the API.**

Example.

```graphql
type Product {

  id: ID!

  name: String!

  price: Float!

}
```

Think of it as:

API contract.

---

# Strong Typing

Every field has a type.

Example.

```graphql
price: Float

name: String

quantity: Int
```

This enables:

- Validation
- Auto-completion
- Documentation
- Safer development

---

# GraphQL Queries

Queries retrieve data.

Example.

```graphql
query {

   product(id:100){

      id

      name

      price

   }

}
```

Only requested fields returned.

---

# GraphQL Response

```json
{
 "data":{
   "product":{
      "id":100,
      "name":"Laptop",
      "price":85000
   }
}
```

---

# GraphQL Mutations

Mutations modify data.

Example.

```graphql
mutation{

 createOrder(

   customerId:10

 ){

   id

   status

 }

}
```

Equivalent to.

```
POST

/orders
```

In REST.

---

# GraphQL Subscriptions

Subscriptions provide.

Real-time updates.

Example.

```graphql
subscription{

   orderUpdated{

      id

      status

   }

}
```

Useful for:

- Live dashboards
- Chat
- Stock prices
- Notifications

---

# GraphQL Resolvers

> **Resolvers are functions responsible for fetching data for each field in a GraphQL query.**

Example.

Client requests.

```graphql
product{

 name

 price

}
```

Resolver retrieves.

Data.

From database.

Or microservices.

---

# Resolver Flow

```text
Client

↓

GraphQL Query

↓

Resolver

↓

Database

↓

Response
```

---

# Benefits

Resolvers can aggregate.

Multiple data sources.

Into one response.

---

# Over-fetching Problem

REST often returns.

Too much data.

Example.

```
GET /customers/100
```

Returns.

```json
{
  "id":100,
  "name":"John",
  "email":"...",
  "phone":"...",
  "address":"...",
  "orders":[...],
  "payments":[...]
}
```

Client only needed.

```
name
```

---

# Under-fetching Problem

REST sometimes returns.

Too little data.

Need.

Multiple requests.

GraphQL solves both.

---

# Advantages

- Flexible responses
- Reduced network traffic
- Strong typing
- Self-documenting
- Better frontend productivity

---

# Challenges

- Complex caching
- Query optimization
- Authorization per field
- More difficult monitoring
- N+1 query problem

---

# N+1 Query Problem

One GraphQL query.

May generate.

Hundreds.

Of database queries.

---

Example.

```
100 Products

↓

Each Product

↓

Separate Seller Query
```

Total.

```
101 Queries
```

Instead of.

```
2
```

---

# Solution

Use batching.

Example.

DataLoader.

Batch related requests together.

---

# GraphQL Federation

Large organizations.

Cannot.

Maintain.

One giant schema.

---

> **GraphQL Federation allows multiple teams to own independent GraphQL services while exposing a unified API.**

---

# Architecture

```text
Client

↓

GraphQL Gateway

↓

Customer Service

Product Service

Order Service

Inventory Service
```

Single API.

Multiple teams.

---

# Benefits

- Independent ownership
- Team autonomy
- Unified client experience

---

# 21. gRPC

> **gRPC is a high-performance Remote Procedure Call (RPC) framework that uses HTTP/2 and Protocol Buffers for efficient communication between services.**

Developed by:

Google.

---

# Why gRPC?

REST.

Uses.

JSON.

Human readable.

But larger.

Slower.

---

gRPC.

Uses.

Binary messages.

Smaller.

Faster.

---

# Architecture

```text
Client

↓

HTTP/2

↓

gRPC Service

↓

Business Logic
```

---

# Characteristics

- Binary protocol
- HTTP/2
- Strong typing
- Contract-first
- Streaming support

---

# Protocol Buffers

> **Protocol Buffers (Protobuf) define the service contract and serialize messages into compact binary format.**

Example.

```proto
message Product{

 int32 id=1;

 string name=2;

 double price=3;

}
```

---

# Why Binary?

Compared to JSON.

Binary.

- Smaller
- Faster
- Less bandwidth
- Better CPU efficiency

---

# gRPC Workflow

```text
.proto File

↓

Code Generation

↓

Client

↓

Server
```

Developers.

Do not manually write.

Serialization code.

---

# gRPC Service Example

```proto
service ProductService{

 rpc GetProduct(ProductRequest)

 returns(ProductResponse);

}
```

---

# Unary RPC

Traditional request-response.

```text
Client

↓

Server

↓

Response
```

Equivalent.

REST.

---

# Server Streaming

One request.

Many responses.

Example.

Stock prices.

```text
Client

↓

Server

↓

Price

↓

Price

↓

Price
```

---

# Client Streaming

Client sends.

Many requests.

One response.

Example.

Large file upload.

---

# Bidirectional Streaming

Both client.

And server.

Send.

Messages.

Simultaneously.

---

Example.

Online gaming.

Video conferencing.

---

# gRPC Streaming Types

| Type | Description |
|--------|-------------|
| Unary | One request → One response |
| Server Streaming | One request → Many responses |
| Client Streaming | Many requests → One response |
| Bidirectional | Many requests ↔ Many responses |

---

# Advantages

- Extremely fast
- Low bandwidth
- Strong contracts
- Streaming support
- Excellent service-to-service communication

---

# Limitations

- Binary format
- Browser support requires additional components (such as gRPC-Web)
- Harder to test manually
- Less human-readable

---

# REST vs GraphQL vs gRPC

| Feature | REST | GraphQL | gRPC |
|----------|------|----------|------|
| Protocol | HTTP | HTTP | HTTP/2 |
| Format | JSON | JSON | Protobuf |
| Multiple Endpoints | Yes | No | Service Methods |
| Client Chooses Fields | No | Yes | No |
| Strong Typing | Limited | Yes | Yes |
| Streaming | Limited | Subscriptions | Native |
| Browser Friendly | Excellent | Excellent | Requires gRPC-Web for browsers |
| Performance | Good | Good | Excellent |

---

# When to Use REST

Choose REST when.

- Public APIs
- CRUD operations
- Simplicity
- Browser compatibility
- Third-party integrations

---

# When to Use GraphQL

Choose GraphQL when.

- Mobile applications
- Complex UI
- Multiple frontend teams
- Frequent UI changes
- Over-fetching becomes a problem

---

# When to Use gRPC

Choose gRPC when.

- Microservice communication
- Low latency
- High throughput
- Internal systems
- Streaming
- Strong contracts

---

# Enterprise Example

Large E-Commerce Platform.

```text
Mobile App

↓

GraphQL

↓

Microservices

↓

gRPC

↓

Database
```

External clients.

Flexible.

Internal communication.

High performance.

---

# API Technology Selection Framework

| Scenario | Best Choice |
|-----------|-------------|
| Public Developer API | REST |
| Partner Integration | REST |
| Mobile Backend | GraphQL |
| Internal Microservices | gRPC |
| Real-Time Dashboard | WebSocket |
| Live Notifications | Server-Sent Events (SSE) |
| Event Processing | Messaging / Async APIs |

---

# Hybrid Architecture

Most enterprise systems.

Use.

Multiple API styles.

Example.

```text
Public API

↓

REST

---------------

Frontend

↓

GraphQL

---------------

Internal

↓

gRPC

---------------

Async

↓

Kafka
```

No single technology.

Solves every problem.

---

# Common Mistakes

### Replacing REST Everywhere with GraphQL

GraphQL is powerful.

But unnecessary for simple CRUD APIs.

---

### Using gRPC for Public APIs

Many external consumers expect HTTP/JSON.

REST is often a better fit.

---

### Ignoring GraphQL Performance

Poor resolver design can cause N+1 query problems.

Optimize with batching and caching.

---

### Mixing API Styles Without Governance

Using REST, GraphQL, and gRPC without clear architectural guidelines leads to inconsistency.

Define when each technology should be used.

---

# Architect's Perspective

Architects should never ask:

> **"Which API technology is the best?"**

Instead ask:

- Who are the consumers?
- What are the latency requirements?
- Is the communication synchronous or asynchronous?
- How frequently do clients change?
- Is streaming required?
- Is browser compatibility important?

The technology should follow the business requirements—not the other way around.

Successful enterprise platforms commonly use:

- **REST** for public APIs
- **GraphQL** for client-driven user interfaces
- **gRPC** for internal service-to-service communication
- **Messaging** for asynchronous workflows

Each technology complements the others rather than replacing them.

---

**End of Part 3**

The next part will cover:

- **WebSockets**
- **Server-Sent Events (SSE)**
- **Webhooks**
- **Async APIs**
- **Real-Time Communication Patterns**
- **REST vs WebSocket vs SSE Decision Framework**
