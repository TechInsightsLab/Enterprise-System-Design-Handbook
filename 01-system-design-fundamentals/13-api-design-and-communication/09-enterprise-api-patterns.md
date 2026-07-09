
# 51. Backend for Frontend (BFF)

> **Backend for Frontend (BFF) is an architectural pattern where each client application has its own dedicated backend optimized for its specific needs.**

Different clients often require different APIs.

Examples:

- Mobile App
- Web Application
- Smart TV
- Smart Watch
- Admin Portal

Using one generic API for all clients usually leads to compromises.

---

# Why BFF?

Imagine an E-Commerce Platform.

Three clients.

```text
Web

Mobile

Admin
```

Need product information.

But differently.

---

## Mobile Needs

```text
Product Name

Price

Image

Rating
```

Small payload.

Fast network.

---

## Web Needs

```text
Product

Reviews

Seller

Inventory

Recommendations
```

More information.

---

## Admin Needs

```text
Product

Inventory

Audit Logs

Supplier

Margin

Sales Analytics
```

Very different.

---

# Without BFF

```text
Clients

↓

Single API

↓

Microservices
```

Problems.

- Large responses
- Complex APIs
- Too many optional fields
- Client-specific logic

---

# With BFF

```text
Web

↓

Web BFF

↓

Microservices

-----------------

Mobile

↓

Mobile BFF

↓

Microservices

-----------------

Admin

↓

Admin BFF

↓

Microservices
```

Each client.

Gets.

Exactly what it needs.

---

# Responsibilities

A BFF typically performs:

- Request aggregation
- Response transformation
- Client-specific validation
- Client-specific authentication integration
- Payload optimization

It should avoid owning core business logic.

---

# Benefits

- Faster frontend development
- Optimized payloads
- Better performance
- Independent evolution
- Simpler client applications

---

# Trade-offs

- Additional services
- Deployment overhead
- Duplicate orchestration logic
- Operational complexity

---

# When to Use

Good candidates:

- Mobile + Web
- IoT
- Smart TVs
- Public APIs
- Admin Portals

Avoid BFF.

If only one client exists.

---

# Enterprise Example

Netflix.

Different UIs.

Need different APIs.

```text
TV

↓

TV BFF

---------------

Mobile

↓

Mobile BFF

---------------

Browser

↓

Web BFF
```

---

# 52. API Composition Pattern

> **API Composition combines data from multiple services into a single response for the client.**

Instead of clients calling:

```text
Customer

↓

Orders

↓

Payments

↓

Recommendations
```

Gateway or BFF composes.

Everything.

Into one response.

---

# Architecture

```text
Client

↓

Composer

├── Customer Service
├── Order Service
├── Payment Service
└── Recommendation Service

↓

Combined Response
```

---

# Example

```json
{
  "customer":{},
  "orders":[],
  "payments":[],
  "recommendations":[]
}
```

One request.

One response.

---

# Benefits

- Lower latency
- Fewer client calls
- Better mobile experience
- Reduced network traffic

---

# Challenges

- Partial failures
- Timeout handling
- Retry complexity
- Aggregation latency

---

# Partial Failure Example

Customer Service.

Success.

Orders.

Success.

Recommendations.

Failed.

Should entire request fail?

Depends.

Business requirements.

---

# Common Strategies

Return.

Partial response.

```json
{
 "customer":{},
 "orders":[],
 "recommendations":null
}
```

Or.

Return fallback.

---

# 53. Aggregator Pattern

The Aggregator Pattern is a specialized form of API Composition.

It collects.

Responses.

From multiple services.

Then combines them.

---

# Workflow

```text
Aggregator

↓

Inventory

↓

Pricing

↓

Shipping

↓

Reviews

↓

Single Response
```

---

# Difference

| API Composition | Aggregator |
|-----------------|------------|
| General concept | Concrete implementation |
| Multiple services | Aggregates responses |

---

# Enterprise Example

Amazon Product Page.

Needs.

- Product
- Inventory
- Price
- Seller
- Reviews

Aggregator.

Builds page.

---

# 54. Facade Pattern

> **A Facade provides a simplified interface over a complex subsystem.**

Unlike Aggregator.

Facade hides complexity.

---

# Example

Without Facade.

Client calls.

```text
Inventory

↓

Pricing

↓

Shipping

↓

Promotion

↓

Tax
```

---

With Facade.

```text
Checkout API

↓

Checkout Service

↓

All Internal Services
```

Client sees.

One API.

---

# Benefits

- Simplified integration
- Reduced client knowledge
- Easier evolution

---

# Difference

Facade.

Hides complexity.

Aggregator.

Combines data.

A service may act as both in some designs.

---

# 55. Anti-Corruption Layer (ACL)

> **An Anti-Corruption Layer protects a modern system from the models and contracts of a legacy or external system.**

Popularized by.

Domain-Driven Design.

---

# Why Needed?

Legacy ERP.

Uses.

```text
CUSTOMER_MASTER_RECORD
```

Modern system.

Uses.

```
Customer
```

Different models.

Need translation.

---

# Architecture

```text
Modern Service

↓

ACL

↓

Legacy System
```

ACL translates.

Requests.

Responses.

Models.

---

# Responsibilities

- Model translation
- Protocol conversion
- Validation
- Error mapping
- Version isolation

---

# Example

Legacy.

```json
{
 "CUST_ID":100
}
```

ACL.

Transforms.

```json
{
 "customerId":100
}
```

Modern services remain clean.

---

# Benefits

- Protects domain model
- Reduces legacy coupling
- Easier migration
- Cleaner architecture

---

# 56. Service Mesh vs API Gateway

Interview question.

Very common.

---

# API Gateway

Handles.

North-South traffic.

```text
Internet

↓

Gateway

↓

Microservices
```

---

# Service Mesh

Handles.

East-West traffic.

```text
Service A

⇄

Service B

⇄

Service C
```

---

# Responsibilities

## API Gateway

- Authentication
- Routing
- Rate limiting
- API versioning
- Request aggregation
- Public APIs

---

## Service Mesh

- mTLS
- Service discovery
- Retry
- Timeout
- Circuit breaker
- Observability
- Traffic shaping

---

# Architecture

```text
Internet

↓

API Gateway

↓

Microservices

⇄

Service Mesh
```

Gateway.

External traffic.

Mesh.

Internal traffic.

---

# Comparison

| API Gateway | Service Mesh |
|-------------|--------------|
| North-South | East-West |
| Client APIs | Service communication |
| Public Entry | Internal Infrastructure |
| API Policies | Service Policies |

---

# 57. North-South vs East-West Traffic

Understanding this distinction is essential.

---

# North-South

Traffic.

Entering.

Or leaving.

Organization.

```text
Client

↓

Gateway

↓

Services
```

---

# East-West

Traffic.

Between services.

```text
Order

↓

Inventory

↓

Payment

↓

Shipping
```

---

# Why Different?

North-South.

Needs.

- Authentication
- Rate limiting
- WAF
- API governance

East-West.

Needs.

- mTLS
- Retry
- Circuit breaker
- Observability
- Service discovery

---

# Enterprise API Patterns

```text
API Patterns

├── Gateway
├── BFF
├── Aggregator
├── Composition
├── Facade
├── Anti-Corruption Layer
├── Adapter
├── Proxy
└── Sidecar (Infrastructure Pattern)
```

Each solves.

Different integration problems.

---

# Choosing the Right Pattern

| Requirement | Pattern |
|-------------|----------|
| Public API Entry | API Gateway |
| Mobile Optimization | BFF |
| Combine Multiple Services | API Composition |
| Single Unified Response | Aggregator |
| Simplify Complex APIs | Facade |
| Integrate Legacy Systems | Anti-Corruption Layer |

---

# Common Mistakes

### Business Logic Inside BFF

BFF should optimize responses.

Business rules belong in domain services.

---

### Aggregator Becoming a God Service

If one aggregator coordinates dozens of workflows,

it becomes difficult to maintain.

Keep aggregation focused.

---

### Gateway Replacing Business Services

Gateway is infrastructure.

Not business logic.

---

### No Anti-Corruption Layer

Allowing legacy models to leak into modern services creates long-term technical debt.

---

### Confusing API Gateway with Service Mesh

They complement each other.

They do not replace each other.

---

# Architect's Perspective

Enterprise APIs are more than HTTP endpoints.

They form an **integration architecture**.

Successful organizations combine multiple patterns:

- API Gateway for external access
- BFF for client optimization
- API Composition for reducing client complexity
- Facades for simplified interfaces
- Anti-Corruption Layers for legacy integration
- Service Mesh for secure service-to-service communication

The architect's responsibility is to ensure that each pattern has a **clear responsibility** and does not become overloaded.

A good API architecture minimizes coupling while maximizing flexibility, scalability, and maintainability.

---

**End of Part 5B**

The next part will cover:

- **OpenAPI**
- **Swagger**
- **AsyncAPI**
- **Contract-First Development**
- **Consumer-Driven Contracts**
- **API Versioning**
- **Backward Compatibility**
- **API Lifecycle Management**
- **API Governance**
- **Enterprise API Standards**
