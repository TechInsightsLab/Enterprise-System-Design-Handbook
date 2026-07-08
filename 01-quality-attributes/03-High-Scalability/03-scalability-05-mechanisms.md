
# 13. Mechanisms

> **Architecture decisions define *what* should be done. Mechanisms define *how* those decisions are implemented using technology.**

Two organizations may implement the same scalable architecture using completely different technology stacks.

For example:

| Architecture Decision | Possible Technologies |
|----------------------|------------------------|
| Load Balancing | NGINX, HAProxy, AWS ALB, Azure Application Gateway |
| Distributed Cache | Redis, Memcached |
| Messaging | Apache Kafka, RabbitMQ, Amazon SQS |
| Auto Scaling | Kubernetes HPA, AWS Auto Scaling, Azure VM Scale Sets |
| Object Storage | Amazon S3, Azure Blob Storage, Google Cloud Storage |
| CDN | CloudFront, Azure CDN, Cloud CDN |
| Database Scaling | PostgreSQL Replication, MySQL Replication, Aurora, Cosmos DB |

Technology evolves rapidly.

Architectural principles remain stable.

---

# Mechanism Selection Flow

```text
Business Requirement

↓

Architecture Decision

↓

Implementation Mechanism

↓

Technology Selection

↓

Monitoring

↓

Continuous Optimization
```

Never start with technology.

Always start with business growth.

---

# 13.1 Load Balancers

Load balancers are one of the earliest scalability mechanisms introduced into production systems.

Instead of directing every request to a single server:

```text
Users

↓

Load Balancer

↓

Server A

Server B

Server C
```

Requests are distributed intelligently.

---

## Why Architects Use Load Balancers

Benefits include:

- Increased throughput
- Better resource utilization
- Improved availability
- Rolling deployments
- Auto Scaling support

---

## Common Algorithms

| Algorithm | Best Use Case |
|-----------|---------------|
| Round Robin | Similar server capacity |
| Least Connections | Long-running requests |
| Weighted Round Robin | Mixed server sizes |
| IP Hash | Session affinity |
| Consistent Hashing | Distributed caching |

---

## Business Example

During a product launch:

```
250,000 Requests/Minute

↓

Load Balancer

↓

25 Servers

↓

10,000 Requests Each
```

No individual server becomes overloaded.

---

# 13.2 Redis

Redis is one of the most common scalability technologies.

Although widely recognized as a cache,

it provides multiple scalability mechanisms.

---

## Common Uses

- Distributed Cache
- Session Store
- Rate Limiting
- Distributed Locking
- Leaderboards
- Pub/Sub

---

## Cache Example

Without Redis:

```text
Request

↓

Database

↓

Response
```

With Redis:

```text
Request

↓

Redis

↓

Response
```

Database load decreases significantly.

---

## Business Benefits

- Lower latency
- Reduced database load
- Higher throughput
- Lower infrastructure cost

---

## Limitations

Redis should accelerate data access.

It should not replace the primary database for critical business records.

---

# 13.3 Relational Databases

Databases often become the first scalability bottleneck.

Scaling strategies include:

---

## Read Replicas

```text
Primary

↓

Replica A

Replica B

Replica C
```

Writes occur only on the primary.

Reads are distributed.

---

## Partitioning

Example:

```text
Orders

↓

2024

2025

2026
```

Each partition becomes smaller and easier to query.

---

## Connection Pooling

Instead of creating new database connections:

```text
Application

↓

Connection Pool

↓

Database
```

Connections are reused.

---

## Business Benefits

- Better throughput
- Reduced latency
- Lower resource consumption

---

# 13.4 Apache Kafka

Kafka enables scalable event streaming.

Instead of tightly coupled communication:

```text
Order Service

↓

Kafka

↓

Inventory

↓

Analytics

↓

Recommendation

↓

Notification
```

---

## Scalability Features

Kafka provides:

- Partitioning
- Consumer Groups
- Horizontal Scaling
- Event Replay
- High Throughput

---

## Parallel Consumption

Example:

```text
Topic

↓

Partition 1

↓

Consumer A

----------------

Partition 2

↓

Consumer B

----------------

Partition 3

↓

Consumer C
```

Throughput increases almost linearly.

---

## Business Benefits

- Decoupling
- Independent scaling
- Massive throughput
- Real-time analytics

---

# 13.5 RabbitMQ

RabbitMQ focuses on reliable task distribution.

Typical workloads:

- Email
- Billing
- Image Processing
- Workflow Automation

---

## Work Queue

```text
Producer

↓

RabbitMQ

↓

Worker 1

Worker 2

Worker 3
```

Workers process tasks independently.

---

## Scaling

Adding workers increases processing capacity.

---

## Business Benefits

- Workload distribution
- Background processing
- Better responsiveness

---

# 13.6 Spring Boot

Spring Boot provides several scalability mechanisms.

---

## Stateless REST APIs

Spring naturally supports stateless architectures.

Example:

```java
@RestController
```

Combined with JWT authentication,

requests remain independent.

---

## Async Processing

Example:

```java
@Async
```

Long-running work executes separately.

---

## Thread Pools

Spring provides configurable executors.

Example:

```text
Incoming Requests

↓

Thread Pool

↓

Business Logic
```

Proper sizing improves throughput.

---

## Caching

Spring Cache supports:

- Redis
- Caffeine
- Hazelcast

Reducing repeated database access.

---

# 13.7 Kubernetes

Kubernetes provides scalable infrastructure management.

---

## Horizontal Pod Autoscaler (HPA)

Example:

```text
CPU

80%

↓

Scale

3 Pods

↓

8 Pods
```

---

## Cluster Autoscaler

When pods cannot be scheduled:

```text
Pods Waiting

↓

New Worker Nodes

↓

Pods Scheduled
```

---

## StatefulSets

Used for:

- Kafka
- Databases
- ZooKeeper

Stateful workloads require stable identities.

---

## Rolling Updates

New application versions are deployed gradually.

Business traffic continues uninterrupted.

---

# 13.8 Amazon Web Services (AWS)

AWS provides managed scalability services.

| Requirement | AWS Service |
|-------------|-------------|
| Auto Scaling | EC2 Auto Scaling |
| Load Balancing | ALB / NLB |
| CDN | CloudFront |
| Messaging | Amazon SQS |
| Event Streaming | Amazon MSK |
| Cache | ElastiCache |
| Database | Aurora / RDS |
| Object Storage | Amazon S3 |

---

## Typical Architecture

```text
Users

↓

CloudFront

↓

Application Load Balancer

↓

EKS

↓

Redis

↓

Aurora

↓

MSK
```

---

# 13.9 Microsoft Azure

Azure provides equivalent scalability capabilities.

| Requirement | Azure Service |
|-------------|---------------|
| Load Balancer | Azure Load Balancer |
| CDN | Azure CDN |
| Kubernetes | AKS |
| Cache | Azure Cache for Redis |
| Messaging | Service Bus |
| Event Streaming | Event Hubs |
| Database | Azure SQL |

---

# 13.10 Google Cloud Platform

Common services include:

| Requirement | GCP Service |
|-------------|-------------|
| Kubernetes | GKE |
| CDN | Cloud CDN |
| Messaging | Pub/Sub |
| Database | Cloud SQL |
| Distributed Database | Spanner |
| Object Storage | Cloud Storage |

---

# 13.11 Distributed Cache

Large systems frequently deploy multiple cache nodes.

```text
Application

↓

Cache Cluster

↓

Node A

Node B

Node C
```

Data is distributed across nodes.

---

## Advantages

- Higher cache capacity
- Better throughput
- Fault isolation

---

## Challenges

- Consistent hashing
- Rebalancing
- Cache invalidation

---

# 13.12 Search Engines

Traditional databases are inefficient for complex search.

Architects introduce dedicated search platforms.

Examples:

- Elasticsearch
- OpenSearch
- Solr

---

## Typical Flow

```text
Application

↓

Search Index

↓

Results
```

---

## Benefits

- Full-text search
- Faster filtering
- Better ranking
- Independent scaling

---

# 13.13 Content Delivery Networks

Global traffic should not always reach the origin server.

Example:

```text
User (India)

↓

Mumbai Edge

↓

Cached Image
```

Instead of:

```text
India

↓

US Origin Server
```

---

## CDN Benefits

- Lower latency
- Reduced bandwidth
- Better scalability
- Lower infrastructure cost

---

# 13.14 Observability

Scalability requires continuous monitoring.

Examples include:

- CPU utilization
- Memory
- Queue depth
- Thread pool usage
- Database latency
- Cache hit ratio
- Network bandwidth

---

## Business Metrics

Monitor:

- Orders per minute
- Payments per second
- Checkout latency
- Search throughput

Business metrics reveal whether scalability supports business growth.

---

# 13.15 Autoscaling Policies

Autoscaling should not rely on CPU alone.

Examples:

| Metric | Suitable Workload |
|---------|-------------------|
| CPU | Compute-heavy |
| Memory | In-memory processing |
| Queue Depth | Messaging |
| RPS | APIs |
| Concurrent Users | Web Applications |
| Business Transactions | Enterprise Systems |

---

# Mechanism Selection Matrix

| Problem | Recommended Mechanism |
|----------|----------------------|
| Uneven Traffic | Load Balancer |
| Slow Database Reads | Redis + Read Replicas |
| High Write Volume | Kafka + Partitioning |
| Background Tasks | RabbitMQ |
| Static Content | CDN |
| Growing Traffic | Kubernetes HPA |
| Database Bottleneck | Sharding |
| Search Latency | Elasticsearch |
| Dynamic Infrastructure | Cloud Auto Scaling |
| Large Files | Object Storage |

---

# Mechanism Comparison

| Mechanism | Primary Benefit | Common Trade-off |
|-----------|-----------------|------------------|
| Load Balancer | Traffic Distribution | Additional Network Hop |
| Redis | Low Latency | Cache Consistency |
| Kafka | High Throughput | Operational Complexity |
| RabbitMQ | Reliable Task Distribution | Lower Throughput Than Kafka |
| Kubernetes | Infrastructure Scalability | Operational Learning Curve |
| CDN | Global Performance | Cache Invalidation |
| Database Replication | Read Scalability | Replication Lag |
| Sharding | Massive Scale | Data Distribution Complexity |

---

# Architect's Perspective

No single technology makes a system scalable.

Real-world systems combine multiple mechanisms.

A global e-commerce platform may include:

- CloudFront for global content delivery
- Application Load Balancers for request distribution
- Kubernetes for container orchestration
- Redis for caching
- Kafka for event streaming
- RabbitMQ for background jobs
- Aurora with read replicas for transactional workloads
- Elasticsearch for product search
- Amazon S3 for media storage

Each technology solves a different scalability problem.

Architects select mechanisms based on workload characteristics, growth projections, operational maturity, and business priorities—not on technology trends.

---

**End of Part 5**

The next section will cover **Trade-offs, Measurements, and Capacity Planning**, including Little's Law in depth, throughput analysis, latency vs throughput, queueing theory, benchmarking, load testing, stress testing, soak testing, scalability metrics, cost optimization, and operational considerations.
