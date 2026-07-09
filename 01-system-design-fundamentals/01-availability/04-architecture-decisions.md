# 13. Architecture Decisions

> *"Architecture is the art of making irreversible decisions with incomplete information."*

High Availability is **not achieved by purchasing better hardware** or deploying to a cloud platform.

It is achieved through a series of architectural decisions that collectively eliminate single points of failure, reduce recovery time, isolate failures, and maintain acceptable service levels under adverse conditions.

This section focuses on **why** architects choose particular architectural patterns.

Implementation technologies (Redis, Kubernetes, Kafka, AWS, Azure, etc.) are discussed in the next section.

---

# Architectural Decision Process

Every architectural decision should answer the following questions:

```text
Business Requirement
        │
        ▼
Identify Business Risks
        │
        ▼
Identify Failure Modes
        │
        ▼
Evaluate Alternatives
        │
        ▼
Choose Architecture Pattern
        │
        ▼
Evaluate Trade-offs
        │
        ▼
Implement Technology
        │
        ▼
Measure Results
```

Notice that **technology selection is the final step**, not the first.

---

# Core Principles Behind High Availability

Before examining individual patterns, architects generally follow five guiding principles.

## Principle 1 — Eliminate Single Points of Failure

If one component can stop the entire platform, the architecture is fragile.

Every critical dependency should have redundancy.

Examples include:

* Application servers
* Databases
* Load balancers
* DNS
* Message brokers
* Network devices
* Storage systems

---

## Principle 2 — Failure Is Normal

Failures should be expected rather than treated as exceptional events.

Instead of asking:

> "Can this fail?"

Architects ask:

> "When this fails, what happens next?"

---

## Principle 3 — Recover Automatically

Manual recovery increases downtime.

Modern systems should recover automatically whenever possible.

Examples:

* Automatic failover
* Auto Scaling
* Kubernetes self-healing
* Automatic leader election
* Automatic DNS failover

---

## Principle 4 — Isolate Failures

A localized failure should remain localized.

Failures should never spread unnecessarily across unrelated services.

---

## Principle 5 — Design for Continuous Operation

Maintenance, deployments, upgrades, and scaling should occur without interrupting customer traffic.

---

# 13.1 Monolith vs Microservices

One of the earliest architectural decisions concerns application decomposition.

## Why Organizations Start with a Monolith

For many businesses, a monolithic application is the correct initial choice.

Advantages include:

* Simpler deployment
* Lower operational complexity
* Easier debugging
* Lower infrastructure costs
* Faster feature development

However, availability challenges emerge as systems grow.

### Availability Risks

```text
            Monolith

      +----------------+
      |                |
      | Entire System  |
      |                |
      +----------------+
              │
              ▼
        Single Deployment

              │
              ▼

      Entire Platform Restarts
```

Even unrelated functionality becomes unavailable during deployments or failures.

---

## Why Organizations Adopt Microservices

Microservices allow independent deployment and failure isolation.

```text
          User Request

               │
               ▼

        API Gateway

   ┌──────┬──────┬──────┐
   ▼      ▼      ▼      ▼

Order  Payment Inventory Search

```

If the Search Service fails:

* Ordering may continue.
* Payments continue.
* Inventory continues.
* Only search functionality is degraded.

Availability improves because failures remain localized.

---

## When NOT to Use Microservices

Microservices introduce:

* Distributed transactions
* Network failures
* Operational complexity
* Increased infrastructure costs
* Observability challenges

For small systems, these costs often outweigh the benefits.

---

# 13.2 Load Balancer

## Why Architects Introduce Load Balancers

A single application server creates a Single Point of Failure.

```text
Users

↓

Application Server

↓

Database
```

If the application server crashes:

Entire service becomes unavailable.

A Load Balancer removes this dependency.

```text
               Users
                  │
                  ▼
         +----------------+
         | Load Balancer  |
         +----------------+
          │      │      │
          ▼      ▼      ▼
        App1   App2   App3
```

---

## Business Benefits

* No single application server failure.
* Horizontal scalability.
* Rolling deployments.
* Automatic traffic redistribution.
* Better resource utilization.

---

## Trade-offs

Advantages

* Improved availability
* Better scalability
* Maintenance without downtime

Disadvantages

* Additional infrastructure
* Health check complexity
* Session management considerations

---

# 13.3 API Gateway

As organizations adopt multiple services, clients should not communicate directly with every backend.

Without an API Gateway:

```text
Mobile App

↓

Order Service

↓

Inventory

↓

Payment

↓

Authentication

↓

Recommendation
```

The client becomes tightly coupled to backend architecture.

---

## Why API Gateways Improve Availability

An API Gateway can:

* Route around failed services.
* Return cached responses.
* Apply rate limiting.
* Authenticate centrally.
* Gracefully degrade unavailable features.

Instead of exposing failures directly to users, the gateway can provide controlled responses.

---

# 13.4 Content Delivery Network (CDN)

Many requests involve static assets:

* Images
* CSS
* JavaScript
* Videos
* Downloads

Serving these directly from origin infrastructure unnecessarily increases risk.

---

## Why Use a CDN?

Benefits include:

* Reduced origin traffic.
* Lower latency.
* Protection against traffic spikes.
* Geographic redundancy.
* Improved resilience during origin degradation.

Even if the origin experiences partial issues, cached content may remain accessible.

---

# 13.5 Caching

Caching is often introduced for performance.

However, it also improves availability.

Without cache:

```text
User

↓

Application

↓

Database
```

Every request depends on the database.

During traffic spikes:

Database becomes overloaded.

Availability decreases.

---

With cache:

```text
User

↓

Application

↓

Redis

↓

Database
```

Most requests never reach the database.

The database survives significantly higher traffic volumes.

---

## Important Principle

Cache should improve performance.

It should never become mandatory for application correctness.

---

# 13.6 Message Queues

Synchronous communication creates strong dependencies.

Example:

```text
Checkout

↓

Payment

↓

Inventory

↓

Notification

↓

Analytics
```

If Notification becomes unavailable:

Entire checkout fails.

---

Message queues decouple services.

```text
Checkout

↓

Queue

↓

Notification
```

Checkout succeeds immediately.

Notification occurs later.

---

## Business Benefits

* Better resilience
* Failure isolation
* Traffic buffering
* Retry capabilities
* Peak traffic smoothing

---

# 13.7 CQRS

Command Query Responsibility Segregation separates read and write workloads.

Instead of:

```text
Users

↓

Single Database
```

Architects separate responsibilities.

```text
Write API

↓

Primary Database

↓

Replication

↓

Read Database

↓

Users
```

Benefits:

* Better read scalability
* Improved availability
* Independent optimization

---

Trade-off:

Greater consistency complexity.

---

# 13.8 Saga Pattern

Distributed transactions spanning multiple services cannot rely on traditional database transactions.

Instead of:

```text
Order

↓

Payment

↓

Inventory

↓

Shipping
```

A Saga coordinates each step independently.

If Shipping fails:

Compensation reverses previous successful operations.

---

Why?

To maintain business consistency while avoiding long-running distributed locks.

---

# 13.9 Circuit Breaker

One slow dependency should not bring down the entire platform.

Without Circuit Breaker:

```text
Application

↓

Slow Payment API

↓

Threads Block

↓

Application Stops
```

---

With Circuit Breaker:

```text
Application

↓

Circuit Opens

↓

Fallback Response
```

Benefits:

* Protects thread pools
* Reduces cascading failures
* Enables graceful degradation
* Accelerates recovery

---

# 13.10 Bulkhead Pattern

Ships remain afloat because water is confined to one compartment.

Applications should behave similarly.

Instead of:

```text
Shared Thread Pool

↓

All Services
```

Use dedicated resource pools.

```text
Order Threads

Payment Threads

Search Threads

Reporting Threads
```

Failure in Reporting should never consume resources required for Payments.

---

# 13.11 Retry

Retries improve resilience against transient failures.

However:

Retries must be:

* Limited
* Intelligent
* Exponential
* Jittered

Otherwise they amplify failures.

---

# 13.12 Timeout

A request should never wait indefinitely.

Every network call should define:

* Connection timeout
* Read timeout
* Write timeout
* Overall request timeout

Timeouts protect:

* Threads
* Connection pools
* CPU
* Memory

---

# 13.13 Rate Limiting

Availability is not only threatened by failures.

It is also threatened by excessive demand.

Rate limiting protects downstream systems.

Example:

```text
100,000 Requests

↓

Rate Limiter

↓

10,000 Allowed

↓

Application
```

Benefits include:

* Fair resource allocation
* Abuse prevention
* Protection against traffic spikes
* Prevention of cascading failures

---

# 13.14 Feature Flags

Sometimes the fastest recovery is disabling a problematic feature.

Rather than redeploying:

Disable:

* Recommendations
* Personalization
* Chat
* Analytics

Core business functions remain available.

---

# 13.15 Blue-Green Deployment

Maintain two production environments.

```text
Blue

(Current)

Green

(New)
```

Traffic switches only after validation.

Rollback becomes nearly instantaneous.

---

# 13.16 Canary Deployment

Instead of exposing all users to a new release:

Deploy gradually.

```text
1%

↓

5%

↓

20%

↓

50%

↓

100%
```

Failures affect only a small subset of users.

---

# 13.17 Multi-AZ Deployment

Applications deployed across multiple Availability Zones survive localized failures.

```text
        Load Balancer

         /          \

      AZ-1        AZ-2

     App A       App B
```

One Availability Zone failure does not interrupt service.

---

# 13.18 Multi-Region Deployment

For mission-critical systems:

```text
Global DNS

↓

US Region

↓

Europe Region

↓

Asia Region
```

Entire regional outages become survivable.

Trade-off:

Significantly increased operational complexity.

---

# 13.19 Active-Active vs Active-Passive

| Active-Active                 | Active-Passive            |
| ----------------------------- | ------------------------- |
| Both sites serve traffic      | One site serves traffic   |
| Higher availability           | Simpler architecture      |
| Better utilization            | Lower infrastructure cost |
| Complex synchronization       | Simpler replication       |
| Higher operational complexity | Longer failover time      |

Architects choose based on Recovery Time Objective (RTO), Recovery Point Objective (RPO), budget, and business criticality.

---

# 13.20 Replication

Replication protects against hardware failures and enables failover.

Common forms include:

* Database replication
* Object storage replication
* File replication
* Configuration replication

Trade-offs:

* Replication lag
* Conflict resolution
* Storage cost

---

# 13.21 Partitioning and Sharding

As data volume grows, distributing data across multiple nodes improves both scalability and availability.

Benefits:

* Reduced contention
* Better fault isolation
* Independent scaling
* Higher throughput

However, poor partition keys can introduce **hot partitions**, reducing overall resilience.

---

# 13.22 Service Discovery

In dynamic environments, service locations change frequently.

Hardcoded addresses reduce availability.

Service discovery enables:

* Dynamic routing
* Automatic registration
* Health-aware request routing

This eliminates configuration drift and simplifies scaling.

---

# 13.23 Health Checks

Health checks allow infrastructure to distinguish healthy instances from unhealthy ones.

Typical checks include:

* Liveness
* Readiness
* Startup
* Dependency health

A load balancer should route traffic only to healthy instances.

---

## Architectural Summary

High Availability is never achieved through a single technology or pattern. It emerges from a collection of deliberate architectural decisions that:

* Eliminate single points of failure.
* Isolate failures before they spread.
* Detect problems quickly.
* Recover automatically whenever possible.
* Degrade gracefully when recovery is not immediate.
* Balance resilience against complexity and cost.

An experienced architect evaluates each decision in the context of business requirements, operational maturity, and acceptable risk—not by applying patterns indiscriminately.

---

**End of Part 4.**

The next part will cover **Section 14: Mechanisms**, where we'll move from architectural reasoning to practical implementation approaches using technologies such as Redis, Kafka, RabbitMQ, Spring Boot, Kubernetes, AWS, Azure, and GCP, followed by the **Decision Matrix** that maps architectural choices to startups, SMEs, enterprises, banking, healthcare, government, and global SaaS environments.
