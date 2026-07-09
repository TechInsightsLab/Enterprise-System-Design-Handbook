# 14. Mechanisms

> **Architecture decisions answer *why*. Mechanisms answer *how*.**

In the previous section, we discussed *why* architects introduce patterns such as load balancing, caching, retries, and circuit breakers.

This section explains the implementation mechanisms commonly used to realize those architectural decisions.

Remember an important architectural principle:

> **Multiple technologies can implement the same architectural mechanism.**

For example:

| Architecture Decision | Possible Technologies                                 |
| --------------------- | ----------------------------------------------------- |
| Cache                 | Redis, Memcached, Hazelcast, Caffeine                 |
| Messaging             | Kafka, RabbitMQ, ActiveMQ, AWS SQS, Azure Service Bus |
| Service Discovery     | Consul, Eureka, Kubernetes DNS                        |
| Load Balancer         | NGINX, HAProxy, AWS ALB, Azure Application Gateway    |
| Monitoring            | Prometheus, Datadog, CloudWatch, Azure Monitor        |

Architects first choose the architectural mechanism.

Technology selection comes afterward.

---

# 14.1 Load Balancing

## Purpose

Distribute traffic across multiple healthy instances.

Without load balancing:

```text
           Users

             │

             ▼

      Application Server

             │

             ▼

          Database
```

Single server failure results in complete outage.

With load balancing:

```text
                 Users

                   │

                   ▼

          Load Balancer

      ┌──────┼──────┐

      ▼      ▼      ▼

    App1   App2   App3

```

Traffic automatically shifts away from unhealthy instances.

---

## Common Technologies

| Platform    | Technology                                                   |
| ----------- | ------------------------------------------------------------ |
| AWS         | Application Load Balancer (ALB), Network Load Balancer (NLB) |
| Azure       | Azure Load Balancer, Application Gateway                     |
| GCP         | Cloud Load Balancing                                         |
| Kubernetes  | Service, Ingress                                             |
| Open Source | HAProxy, NGINX, Envoy                                        |

---

## Key Mechanisms

* Health Checks
* Sticky Sessions (when required)
* Weighted Routing
* Least Connections
* Round Robin
* IP Hash
* Connection Draining

---

# 14.2 Health Checks

Load balancers require a mechanism to determine whether an instance should receive traffic.

Typical checks include:

## Liveness Probe

Answers:

> Is the application process alive?

---

## Readiness Probe

Answers:

> Is the application ready to receive requests?

---

## Startup Probe

Answers:

> Has the application finished initialization?

---

Example:

```text
Request

↓

Load Balancer

↓

Health Check

↓

Healthy?

↓

Yes → Send Traffic

↓

No → Remove Instance
```

---

# 14.3 Database Replication

Databases often represent the most critical dependency.

Replication increases availability.

## Primary-Replica Architecture

```text
                Primary

               /      \

              ▼        ▼

         Replica1   Replica2
```

Benefits:

* Read scalability
* Backup availability
* Automatic failover
* Reduced recovery time

---

## Replication Types

| Type             | Characteristics                              |
| ---------------- | -------------------------------------------- |
| Synchronous      | Strong consistency, higher latency           |
| Asynchronous     | Better performance, possible replication lag |
| Semi-Synchronous | Balanced approach                            |

---

## Business Trade-off

Synchronous replication increases consistency.

Asynchronous replication increases availability.

---

# 14.4 Redis

Redis is frequently introduced as a distributed cache.

It reduces:

* Database load
* Response time
* Infrastructure cost

Example:

```text
User

↓

Application

↓

Redis

↓

Database
```

Common use cases:

* Session storage
* Product catalog cache
* Frequently accessed configuration
* Rate limiting
* Distributed locking

---

## High Availability Mechanisms

Redis supports:

* Replication
* Sentinel
* Redis Cluster
* Automatic Failover

---

## Important Design Principle

Redis should accelerate requests.

Applications should continue functioning if Redis becomes temporarily unavailable.

---

# 14.5 Kafka

Kafka improves availability through asynchronous communication.

Instead of:

```text
Order

↓

Inventory

↓

Notification
```

Use:

```text
Order

↓

Kafka Topic

↓

Inventory

↓

Notification

↓

Analytics
```

Benefits:

* Loose coupling
* Independent scaling
* Replay capability
* Failure isolation
* Traffic buffering

---

## Availability Mechanisms

Kafka achieves resilience through:

* Partition replication
* Leader election
* ISR (In-Sync Replicas)
* Broker redundancy
* Consumer groups

---

# 14.6 RabbitMQ

RabbitMQ focuses on reliable message delivery.

Typical use cases:

* Email
* Notifications
* Payment processing
* Order fulfillment
* Workflow orchestration

Availability mechanisms:

* Mirrored queues
* Quorum queues
* Persistent messages
* Dead-letter queues

---

# 14.7 Spring Boot

Spring Boot provides several mechanisms supporting High Availability.

Examples include:

### Health Endpoints

```text
/actuator/health
```

---

### Graceful Shutdown

Allows in-flight requests to complete before termination.

---

### Retry Support

Using Spring Retry.

---

### Circuit Breakers

Using libraries such as:

* Resilience4j
* Spring Cloud Circuit Breaker

---

### Metrics

Spring Boot Actuator exports:

* CPU
* Memory
* HTTP Metrics
* JVM Metrics
* Thread Pools
* Connection Pools

---

# 14.8 Kubernetes

Kubernetes provides numerous availability mechanisms.

---

## Self-Healing

If a container crashes:

```text
Pod Crash

↓

Kubernetes

↓

Automatically Creates New Pod
```

---

## ReplicaSets

Multiple replicas eliminate single pod failures.

```text
Desired Replicas = 5

↓

One Pod Dies

↓

Kubernetes Creates Replacement
```

---

## Rolling Updates

Applications update incrementally.

Customers continue using healthy instances.

---

## Pod Disruption Budgets

Prevent excessive simultaneous pod termination.

---

## Horizontal Pod Autoscaler

Automatically increases replicas under load.

---

## StatefulSets

Used for stateful applications requiring stable identities.

---

# 14.9 AWS

AWS provides managed services implementing High Availability principles.

| Requirement    | AWS Service                        |
| -------------- | ---------------------------------- |
| Compute        | EC2 Auto Scaling, ECS, EKS, Lambda |
| Load Balancing | ALB, NLB                           |
| DNS            | Route 53                           |
| Messaging      | SQS, SNS, MSK                      |
| Cache          | ElastiCache                        |
| Database       | RDS Multi-AZ, Aurora               |
| Monitoring     | CloudWatch                         |
| Object Storage | S3                                 |

---

## Common AWS Availability Architecture

```text
Internet

↓

Route53

↓

Application Load Balancer

↓

Multiple Availability Zones

↓

Auto Scaling Group

↓

Multi-AZ Database
```

---

# 14.10 Azure

Azure provides equivalent availability mechanisms.

Examples include:

| Requirement | Azure Service              |
| ----------- | -------------------------- |
| Compute     | Virtual Machine Scale Sets |
| Containers  | AKS                        |
| Database    | Azure SQL, Cosmos DB       |
| Cache       | Azure Cache for Redis      |
| Messaging   | Service Bus                |
| DNS         | Azure DNS                  |
| Monitoring  | Azure Monitor              |

---

# 14.11 Google Cloud Platform

GCP provides managed mechanisms including:

| Requirement   | GCP Service          |
| ------------- | -------------------- |
| Compute       | Compute Engine       |
| Kubernetes    | GKE                  |
| Messaging     | Pub/Sub              |
| Database      | Cloud SQL, Spanner   |
| Load Balancer | Cloud Load Balancing |
| Monitoring    | Cloud Monitoring     |

---

# 14.12 Observability

Availability cannot be managed without visibility.

Core observability pillars include:

## Metrics

Examples:

* CPU
* Memory
* Requests/sec
* Error Rate
* Queue Depth

---

## Logs

Used for:

* Incident investigation
* Auditing
* Debugging

---

## Traces

Distributed tracing identifies:

* Slow services
* Network latency
* Dependency bottlenecks

---

# 14.13 Service Mesh

Modern distributed systems often introduce a service mesh.

Examples:

* Istio
* Linkerd
* Consul Connect

Capabilities include:

* Automatic retries
* Mutual TLS
* Traffic shifting
* Circuit breakers
* Observability
* Fault injection

---

# Mechanism Selection Guidelines

| Requirement               | Preferred Mechanism        |
| ------------------------- | -------------------------- |
| Remove SPOF               | Load Balancer              |
| Reduce Database Load      | Cache                      |
| Decouple Services         | Message Queue              |
| Automatic Recovery        | Kubernetes                 |
| Cross-Region Availability | Global DNS                 |
| Traffic Distribution      | CDN                        |
| Fast Recovery             | Auto Scaling               |
| Fault Isolation           | Circuit Breaker + Bulkhead |
| Read Scaling              | Replication                |
| Deployment Availability   | Blue-Green / Canary        |

---

# 15. Decision Matrix

Every organization has different business constraints.

The correct architecture depends on:

* Budget
* Team maturity
* Operational expertise
* Regulatory requirements
* Customer expectations
* Business criticality

There is no universally correct solution.

---

## Decision Matrix

| Scenario                        | Recommendation                                                                                       | Why                                                           | When Not                                          |
| ------------------------------- | ---------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------------- |
| Startup MVP                     | Monolith + Load Balancer + Automated Backups                                                         | Fast delivery with basic resilience                           | Mission-critical or regulated systems             |
| Small & Medium Enterprise (SME) | Modular Monolith or Small Microservices + Cache + Managed Database                                   | Balance simplicity and availability                           | Highly distributed, globally scaled platforms     |
| Enterprise                      | Microservices + API Gateway + Circuit Breaker + Multi-AZ Deployment                                  | Independent scaling and failure isolation                     | Small teams lacking operational maturity          |
| Banking                         | Multi-AZ, Active-Passive or Active-Active, Synchronous Replication, Strong Monitoring                | High regulatory requirements, financial risk, strict SLAs     | Low-budget or non-critical internal applications  |
| Healthcare                      | Multi-AZ, Disaster Recovery, Audit Logging, Redundant Databases                                      | Patient safety, regulatory compliance, continuous access      | Prototype or experimental systems                 |
| Government                      | High Availability with Disaster Recovery, Multi-Region for critical services, Immutable Audit Trails | Public service continuity and compliance                      | Low-impact internal administrative tools          |
| Global SaaS                     | Multi-Region, CDN, Global Load Balancing, Auto Scaling, Asynchronous Messaging                       | Worldwide users, low latency, resilience to regional failures | Single-region products with a local customer base |

---

## Decision Tree

```text
                    Business Critical?

                     /            \

                  Yes              No

                  │                │

          Downtime Acceptable?     Monolith

            /          \

         No             Yes

         │               │

   Multi-AZ        Single Region

         │

   Global Customers?

      /         \

    Yes         No

    │            │

Multi-Region   Multi-AZ

    │

Need Near-Zero Downtime?

     /            \

   Yes             No

   │                │

Active-Active   Active-Passive
```

---

## Key Takeaways

* There is no "best" architecture—only the architecture that best fits the business context.
* Mechanisms such as load balancing, replication, caching, messaging, and orchestration are tools to realize architectural decisions, not goals in themselves.
* The maturity of the engineering organization is as important as the technology stack. A simpler architecture that the team can operate reliably is often more available than a sophisticated architecture that is poorly understood.
* Availability is achieved through the combination of sound architecture, robust implementation mechanisms, operational excellence, and continuous measurement.

---

**End of Part 5.**

The next part will cover:

* **16. Trade-offs**
* **17. Measurements (SLI, SLO, SLA, Availability calculations, MTTR, MTBF, RTO, RPO, P95/P99, Throughput, Queue metrics, Cache hit ratio, etc.)**
* **18. Operational Considerations** (Monitoring, Logging, Tracing, Alerting, Capacity Planning, Chaos Engineering, Disaster Recovery, Cost Monitoring).
