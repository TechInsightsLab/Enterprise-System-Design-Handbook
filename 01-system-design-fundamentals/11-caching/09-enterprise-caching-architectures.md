
# 63. Caching in Microservices

> **In a microservices architecture, caching reduces latency, minimizes inter-service communication, protects downstream services, and improves overall system scalability.**

Unlike a monolithic application,

each microservice owns:

- Its own database
- Its own APIs
- Its own scaling

This also means:

Each service must decide:

- What to cache
- Where to cache
- How to invalidate cache

---

# Why Microservices Need Caching

Consider an E-Commerce Platform.

```text
Product Service

↓

Inventory Service

↓

Pricing Service

↓

Recommendation Service

↓

Review Service
```

Rendering a single product page may involve multiple service calls.

Without caching,

every request triggers several network calls.

---

# Without Cache

```text
Client

↓

Product Service

↓

Inventory Service

↓

Pricing Service

↓

Review Service

↓

Recommendation Service

↓

Database
```

Many network hops.

Higher latency.

---

# With Cache

```text
Client

↓

Product Service

↓

Redis

↓

Inventory Cache

↓

Pricing Cache

↓

Review Cache
```

Many backend calls eliminated.

---

# Benefits

- Lower latency
- Reduced service dependencies
- Lower database load
- Better fault isolation
- Improved throughput

---

# Service-Level Cache

Each service maintains its own cache.

```text
Product Service

↓

Redis

-------------------

Inventory Service

↓

Redis

-------------------

Pricing Service

↓

Redis
```

Each cache contains only data owned by that service.

---

# Why Not Share One Redis?

Many beginners design:

```text
All Services

↓

One Redis
```

Initially simple.

Eventually problematic.

---

# Problems

- No ownership
- Key collisions
- Security concerns
- Operational complexity
- Difficult scaling
- No clear cache lifecycle

---

# Better Design

Prefer:

```text
Product Service

↓

Product Cache

--------------------

Inventory Service

↓

Inventory Cache

--------------------

Pricing Service

↓

Pricing Cache
```

Each bounded context owns its cache.

This aligns with Domain-Driven Design (DDD).

---

# 64. API Gateway Caching

API Gateways can cache responses before requests reach backend services.

---

# Architecture

```text
Client

↓

API Gateway

↓

Cache

↓

Microservices
```

---

# Example

Popular API.

```
GET /products
```

Thousands of users.

Gateway returns:

Cached response.

Backend untouched.

---

# Suitable APIs

- Product Catalog
- Categories
- Public APIs
- Country Lists
- Exchange Rates

---

# Unsuitable APIs

- Payments
- User Balances
- OTP Verification
- Highly personalized responses (unless cache keys include user-specific context)

---

# Advantages

- Lower latency
- Reduced backend traffic
- Better scalability
- Lower cloud cost

---

# Common API Gateways

- Kong
- NGINX
- Envoy
- AWS API Gateway
- Azure API Management

Many support configurable response caching.

---

# 65. Database Query Caching

Instead of caching:

Business objects,

cache:

Query results.

---

Example.

```sql
SELECT

Top 10 Products
```

Result reused.

---

# Workflow

```text
Request

↓

Redis

↓

Cached Query Result
```

---

# Suitable For

- Dashboard
- Trending Products
- Reports
- Read-only APIs

---

# Unsuitable For

Highly dynamic queries.

Example.

```
Inventory

Every Second
```

---

# Query Cache vs Object Cache

| Query Cache | Object Cache |
|--------------|--------------|
| Stores result set | Stores business object |
| Query-specific | Entity-specific |
| Sensitive to SQL changes | Stable business model |

---

# 66. GraphQL Caching

GraphQL allows clients to request:

Exactly the fields needed.

Caching therefore requires more planning.

---

# Challenge

Client A requests:

```graphql
name

price
```

Client B requests:

```graphql
name

price

reviews
```

Responses differ.

Simple URL-based caching becomes less effective.

---

# Solutions

- Persisted Queries
- Resolver-level cache
- Object cache
- CDN for persisted queries
- DataLoader for request-scoped deduplication

---

# Resolver Cache

Each resolver caches independently.

Example.

```text
Product Resolver

↓

Redis

↓

Database
```

Different fields cached separately.

---

# 67. CDN Architecture

A CDN is one of the largest cache layers.

---

# Architecture

```text
User

↓

Nearest Edge

↓

Origin

↓

Application

↓

Database
```

---

# Example

Image.

Hosted:

Mumbai.

User:

Canada.

Without CDN.

```
Canada

↓

Mumbai
```

---

With CDN.

```
Canada

↓

Toronto Edge
```

Latency significantly reduced.

---

# CDN Cache Levels

```text
Browser

↓

ISP Cache (where applicable)

↓

CDN Edge

↓

Origin
```

Multiple layers.

---

# CDN Invalidation

Content changes.

Need refresh.

Possible approaches.

- Purge
- Versioning
- TTL
- Cache-Control headers

---

# Enterprise Recommendation

Versioned assets.

Example.

```
logo.v25.png
```

instead of:

```
logo.png
```

Old cache naturally expires.

---

# 68. Cloud Cache Services

Major cloud providers offer managed caching services.

---

# AWS

## Amazon ElastiCache

Supports.

- Redis
- Memcached

---

Features.

- Automatic failover
- Monitoring
- Scaling
- Backups (Redis)
- Encryption
- IAM integration

---

# Azure

## Azure Cache for Redis

Features.

- Redis Enterprise options
- Geo-replication (selected tiers)
- Monitoring
- High Availability
- Private networking

---

# Google Cloud

## Memorystore

Supports.

- Redis
- Memcached (depending on service offering)

Managed operations.

---

# Comparison

| Cloud | Service |
|--------|----------|
| AWS | ElastiCache |
| Azure | Azure Cache for Redis |
| Google Cloud | Memorystore |

---

# Why Managed Cache?

Instead of managing:

- Installation
- Failover
- Patching
- Monitoring

Cloud automates:

Operations.

---

# 69. Event-Driven Cache Invalidation

Large enterprises rarely rely only on TTL.

Instead.

Events.

---

# Architecture

```text
Database

↓

CDC

↓

Kafka

↓

Consumers

↓

Redis

↓

Application
```

---

# Example

Product updated.

Publish.

```
ProductUpdated
```

Every service.

Invalidates.

Its cache.

---

# Advantages

- Near real-time
- Decoupled
- Independent services
- Better scalability

---

# Technologies

- Kafka
- RabbitMQ
- Pulsar
- Redis Pub/Sub (for lightweight scenarios)
- Cloud-native event buses

---

# 70. Multi-Region Cache

Global systems require:

Regional caches.

---

Architecture

```text
Asia

↓

Redis

----------------

Europe

↓

Redis

----------------

USA

↓

Redis
```

Users access:

Nearest cache.

---

# Benefits

- Lower latency
- Better availability
- Regional resilience

---

# Challenges

Data synchronization.

Product updated.

Asia.

Europe.

Still old.

---

# Possible Strategies

- Async replication
- Regional ownership
- Event propagation
- Versioning
- Short TTL

---

# 71. Enterprise Caching Architecture

A modern enterprise rarely uses one cache.

Instead.

```text
Browser Cache

↓

CDN

↓

API Gateway Cache

↓

Application Cache

↓

Redis

↓

Database Buffer Pool

↓

Database
```

Every layer contributes.

---

# Example

Customer requests.

```
Laptop
```

Workflow.

```text
Browser

↓

CDN

↓

Gateway

↓

Application

↓

Redis

↓

PostgreSQL
```

Only if every cache misses,

database queried.

---

# Enterprise E-Commerce Architecture

```text
                Users
                  │
        ┌─────────┴─────────┐
        │                   │
     Browser Cache       Mobile App Cache
        │                   │
        └─────────┬─────────┘
                  │
             CDN / Edge Cache
                  │
           API Gateway Cache
                  │
     ┌────────────┼────────────┐
     │            │            │
 Product      Inventory     Pricing
 Service       Service      Service
     │            │            │
  Local L1     Local L1     Local L1
   Cache         Cache        Cache
     │            │            │
     └────────────┼────────────┘
                  │
          Shared Redis Cluster
                  │
        Read Replicas / Primary DB
                  │
             Persistent Storage
```

This architecture reduces:

- Network latency
- Database load
- Cross-service communication

while maintaining scalability.

---

# Caching in Kubernetes

In Kubernetes,

Redis is usually deployed as:

- Managed cloud service (preferred)
- StatefulSet (self-managed)
- Redis Operator

Applications remain stateless.

```text
Pods

↓

Redis Service

↓

Redis Cluster
```

Pods can scale independently.

---

# Sidecar Cache Pattern

Some architectures introduce:

Local cache sidecars.

```text
Application

↓

Sidecar Cache

↓

Redis
```

Benefits.

- Reduced network latency
- Local cache
- Independent lifecycle

Often used only for specialized workloads because it increases operational complexity.

---

# Common Mistakes

### Caching Across Bounded Contexts

Product Service directly updates Inventory cache.

Violates ownership.

Each service should own its own cache.

---

### Gateway Caches Personalized Responses

Risk of serving one user's data to another.

Always include appropriate cache keys and authorization awareness.

---

### Ignoring CDN

Static resources repeatedly served by application servers.

Unnecessary backend load.

---

### Large Shared Redis for Everything

Creates operational bottlenecks.

Prefer logical separation by domain, workload, or environment.

---

### Event Without Monitoring

Cache invalidation events stop.

Stale data spreads across services.

Monitor event pipelines and consumer lag.

---

# Architect's Perspective

Caching in microservices is **not only about performance**.

It is about **service autonomy**.

Every microservice should:

- Own its cache.
- Own its invalidation strategy.
- Own its consistency guarantees.

Enterprise architects should avoid creating:

> **A giant shared cache that becomes another distributed monolith.**

Instead,

align cache ownership with:

- Service boundaries
- Business domains
- Data ownership

This produces architectures that are easier to:

- Scale
- Operate
- Secure
- Evolve

while preserving the independence that microservices are intended to provide.

---

**End of Part 9**

The next part will cover:

- **Cache Observability**
- **Cache Metrics (Hit Ratio, Miss Ratio, Evictions, Latency)**
- **Redis Monitoring**
- **Capacity Planning**
- **Cost Optimization**
- **Security Best Practices**
- **Production Incidents**
- **Architecture Review Checklist**
- **ADR**
- **Enterprise Case Studies**
