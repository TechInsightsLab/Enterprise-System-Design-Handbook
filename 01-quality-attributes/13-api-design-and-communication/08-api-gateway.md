
# 39. API Gateway

> **An API Gateway is a centralized entry point that receives client requests, applies cross-cutting concerns, and routes them to the appropriate backend services.**

In a Microservices architecture,

clients should **not** communicate directly with every service.

Instead,

all requests pass through an API Gateway.

---

# Why API Gateway?

Imagine an e-commerce platform with:

- Customer Service
- Order Service
- Inventory Service
- Payment Service
- Shipping Service
- Notification Service

Without an API Gateway.

Clients must know:

- Every service URL
- Authentication mechanism
- Version
- Routing logic

Architecture.

```text
Mobile App

├── Customer Service
├── Order Service
├── Inventory Service
├── Payment Service
└── Shipping Service
```

This creates:

- Tight coupling
- Security risks
- Complex clients
- Difficult maintenance

---

# API Gateway Solution

```text
Client

↓

API Gateway

├── Customer Service
├── Order Service
├── Inventory Service
├── Payment Service
└── Shipping Service
```

Clients communicate with only one endpoint.

---

# Responsibilities

An API Gateway typically provides:

```text
API Gateway

├── Routing
├── Authentication
├── Authorization
├── Rate Limiting
├── SSL Termination
├── Request Validation
├── Caching
├── Monitoring
├── Logging
├── Load Balancing
├── Request Transformation
├── Response Transformation
└── API Versioning
```

Notice that most of these are **cross-cutting concerns**.

---

# Why Not Implement These in Every Service?

Imagine.

50 Microservices.

Each implements:

- Authentication
- Logging
- Rate Limiting
- CORS
- Metrics

Result.

Massive code duplication.

Gateway centralizes them.

---

# Request Flow

```text
Browser

↓

API Gateway

↓

Authentication

↓

Routing

↓

Order Service

↓

Database

↓

Response
```

---

# Benefits

- Single Entry Point
- Better Security
- Simplified Clients
- Centralized Policies
- Easier Monitoring
- Consistent API Experience

---

# Trade-offs

- Additional network hop
- Gateway becomes critical infrastructure
- Potential bottleneck if poorly designed
- Operational complexity

Architects should deploy gateways in a highly available configuration.

---

# 40. Request Routing

One of the primary responsibilities of an API Gateway is routing.

Incoming requests are forwarded to the correct backend service.

---

# Example

```http
GET /customers/100
```

↓

Customer Service

---

```http
POST /orders
```

↓

Order Service

---

```http
GET /products
```

↓

Product Service

---

# Routing Architecture

```text
API Gateway

├── /customers → Customer Service
├── /orders → Order Service
├── /products → Product Service
└── /payments → Payment Service
```

---

# Routing Strategies

Gateways can route based on:

- URL path
- HTTP method
- Headers
- Host name
- API version
- Geographic region
- Tenant

---

# Example

```text
/api/v1/orders

↓

Order Service V1
```

---

```text
/api/v2/orders

↓

Order Service V2
```

---

# Canary Routing

Deploy new version.

Only.

5% traffic.

```text
95%

↓

V1

5%

↓

V2
```

Useful.

For gradual rollout.

---

# Blue-Green Routing

Two complete environments.

```text
Blue

↓

Current

------------

Green

↓

New Version
```

Traffic switches.

Instantly.

Rollback becomes easier.

---

# 41. Authentication at Gateway

Instead of every service validating JWT.

Gateway performs authentication.

---

# Workflow

```text
Client

↓

JWT

↓

Gateway

↓

Validate

↓

Forward Request

↓

Microservice
```

---

# Benefits

- Centralized authentication
- Less duplicated code
- Faster development
- Consistent security

---

# Should Services Trust the Gateway?

For internal architectures.

Usually.

Yes.

But critical services often perform lightweight validation or verify forwarded identity headers to support defense in depth.

---

# Identity Propagation

Gateway forwards identity.

Example.

```http
X-User-ID: 101

X-User-Role: ADMIN
```

Services receive.

Trusted identity.

Without parsing JWT again.

(Implementation depends on organizational security policies.)

---

# 42. Authorization at Gateway

Gateway can perform.

Coarse-grained authorization.

Example.

```
Customer

↓

Cannot

/admin/*
```

---

Fine-grained business authorization.

Usually belongs.

Inside.

Business service.

---

# Rule of Thumb

Gateway.

Protects.

Platform.

Service.

Protects.

Business rules.

---

# Example

Gateway checks.

```
Authenticated?
```

Order Service checks.

```
Owns This Order?
```

Both are needed.

---

# 43. SSL/TLS Termination

HTTPS encryption.

Consumes CPU.

Gateway can terminate TLS.

---

Architecture.

```text
Client

⇄ HTTPS ⇄

Gateway

↓

HTTP / HTTPS

↓

Services
```

Many organizations continue using HTTPS internally as well, especially in Zero Trust environments.

---

# Benefits

- Simplifies certificate management
- Centralized TLS configuration
- Improved operational efficiency

---

# 44. Caching at Gateway

Gateway may cache.

Frequently requested responses.

---

Example.

```
GET

/products
```

Thousands.

Of requests.

Daily.

Gateway caches.

Response.

---

Architecture.

```text
Client

↓

Gateway Cache

↓

Hit?

↓

Yes

↓

Return

--------------

No

↓

Service
```

---

# Good Candidates

- Product Catalog
- Country List
- Currency List
- Configuration
- Static Metadata

---

# Avoid Caching

- Payments
- Banking Transactions
- Real-time Inventory
- Sensitive User Data (unless carefully controlled)

---

# Benefits

- Reduced latency
- Lower backend load
- Lower infrastructure cost

---

# 45. Rate Limiting at Gateway

Gateway is the ideal place.

To enforce rate limits.

---

Example.

```
100 Requests

Per Minute

Per User
```

---

Request.

```
101
```

↓

```
429

Too Many Requests
```

Backend never receives abusive traffic.

---

# Rate Limiting Dimensions

Gateway may limit by:

- User
- API Key
- IP Address
- Tenant
- OAuth Client
- Subscription Tier

---

# Enterprise Example

Free Tier.

```
1000

Requests/Day
```

Premium.

```
100000

Requests/Day
```

Different plans.

Different limits.

---

# 46. Request Aggregation

Without Gateway.

Mobile app requests.

```text
Customer

↓

Orders

↓

Payments

↓

Recommendations
```

Four requests.

---

Gateway.

Aggregates.

```text
Client

↓

Gateway

↓

4 Services

↓

Single Response
```

---

# Benefits

- Lower latency
- Fewer network calls
- Better mobile performance

---

# Example Response

```json
{
  "customer":{},
  "orders":[],
  "recommendations":[],
  "payments":[]
}
```

One API.

Multiple services.

---

# Trade-offs

Aggregation increases gateway complexity.

Avoid placing business logic inside the gateway.

It should orchestrate responses—not become a business service.

---

# 47. Response Transformation

Sometimes.

Different clients.

Need different formats.

Gateway can transform.

Responses.

---

Example.

Backend.

```json
{
 "firstName":"John",
 "lastName":"Doe"
}
```

Gateway.

```json
{
 "name":"John Doe"
}
```

Useful.

For compatibility.

---

# Request Transformation

Gateway can also transform.

Incoming requests.

Examples.

- Header normalization
- Field mapping
- Protocol translation
- Version adaptation

---

# 48. Load Balancing

Gateway distributes requests.

Across multiple instances.

---

Architecture.

```text
Gateway

↓

Order1

Order2

Order3
```

Requests distributed.

Automatically.

---

# Common Algorithms

- Round Robin
- Least Connections
- Weighted Round Robin
- Consistent Hashing

Choice depends on workload characteristics.

---

# 49. Service Discovery

Microservices scale dynamically.

Instances appear.

Disappear.

Gateway should not rely on static IP addresses.

---

Architecture.

```text
Gateway

↓

Service Registry

↓

Current Instances

↓

Route Request
```

---

# Popular Registries

- Kubernetes Services
- Consul
- Eureka
- etcd
- Cloud-native service discovery

---

# Benefits

- Dynamic routing
- Autoscaling support
- High availability

---

# 50. API Versioning at Gateway

Gateway can expose.

Multiple versions.

---

Example.

```text
/api/v1/orders

/api/v2/orders
```

Routes.

To different services.

Or.

Different service versions.

---

# Benefits

- Backward compatibility
- Controlled migration
- Independent evolution

---

# Common Mistakes

### Putting Business Logic Inside Gateway

Gateway should handle:

- Routing
- Security
- Policies

Not.

Business workflows.

---

### Single Gateway Instance

Creates.

Single Point of Failure.

Always deploy multiple gateway instances behind a load balancer.

---

### Gateway Calling Databases

Gateway should communicate with services.

Not databases.

Maintain clear architectural boundaries.

---

### Excessive Request Aggregation

Overly complex aggregation increases latency and reduces maintainability.

Aggregate only where it delivers clear business value.

---

### Ignoring Gateway Observability

Monitor:

- Request rate
- Error rate
- Latency
- Authentication failures
- Rate limit violations
- Backend health

The gateway is often the best observation point for API traffic.

---

# Architect's Perspective

An API Gateway is **not just a reverse proxy**.

It is the **policy enforcement point** for an organization's APIs.

Its responsibilities include:

- Security
- Routing
- Governance
- Traffic management
- Operational visibility

However,

architects should resist the temptation to turn the gateway into a **God Service**.

Business logic belongs inside domain services.

The gateway should remain focused on infrastructure and cross-cutting concerns.

---

**End of Part 5A**

The next part will cover:

- **Backend for Frontend (BFF)**
- **API Composition Pattern**
- **Aggregator Pattern**
- **Facade Pattern**
- **Anti-Corruption Layer (ACL)**
- **Service Mesh vs API Gateway**
- **North-South vs East-West Traffic**
- **Enterprise API Patterns**
