
# 12. Architecture Decisions

> **Scalability is achieved through architectural decisions, not infrastructure upgrades alone.**

When architects design scalable systems, they rarely begin by choosing Kubernetes, Redis, or cloud services.

Instead, they ask:

- What limits growth?
- Where are the bottlenecks?
- Can work be distributed?
- Can state be separated?
- Can requests be processed independently?

Only after answering these questions do they select technologies.

---

# Scalability Decision Framework

```text
Business Growth

↓

Growth Dimension

↓

Identify Bottleneck

↓

Choose Architecture Pattern

↓

Evaluate Trade-offs

↓

Implement Technology

↓

Measure

↓

Optimize
```

Every scalability decision should begin with business growth rather than infrastructure.

---

# 12.1 Stateless Architecture

One of the most important scalability decisions.

---

## Why Stateless Services Exist

Imagine a web application running on one server.

```
User

↓

Server

↓

Session Stored in Memory
```

As traffic grows:

```
User

↓

Load Balancer

↓

Server A

Server B

Server C
```

Which server contains the user's session?

Without shared session management,

Requests fail.

---

## Stateless Principle

Every request should contain enough information to be processed independently.

Example:

```
JWT Token

↓

Authentication

↓

Business Logic

↓

Response
```

No session memory is required.

---

## Benefits

- Horizontal Scaling
- Simpler Load Balancing
- Easier Deployment
- Better Fault Tolerance
- Faster Recovery

---

## Business Impact

Stateless services enable adding servers without changing application behavior.

---

# 12.2 Horizontal Scaling

Horizontal Scaling means:

Adding more machines.

```
Before

1 Server
```

↓

```
After

10 Servers
```

---

## Business Benefits

- Higher throughput
- Better availability
- Incremental growth
- Lower failure impact

---

## Challenges

- Distributed communication
- Session management
- Cache synchronization
- Data consistency

---

## Typical Use Cases

- APIs
- Web servers
- Microservices
- Search services

---

# 12.3 Vertical Scaling

Vertical Scaling means:

Increasing resources on one machine.

```
4 CPU

↓

8 CPU

↓

32 CPU
```

---

## Advantages

- Simplicity
- Minimal code changes
- Easier debugging
- Strong database performance

---

## Disadvantages

- Hardware limits
- Downtime for upgrades
- Higher infrastructure cost
- Single failure domain

---

## Business Perspective

Vertical scaling is often the fastest short-term solution.

Horizontal scaling provides better long-term growth.

---

# Horizontal vs Vertical Scaling

| Horizontal | Vertical |
|------------|----------|
| Add servers | Add resources |
| Better fault tolerance | Simpler management |
| Better long-term scalability | Faster implementation |
| Distributed complexity | Hardware limits |

---

# 12.4 Load Balancing

Load balancing distributes requests across multiple resources.

```
Users

↓

Load Balancer

↓

Server A

Server B

Server C
```

---

## Why Architects Use Load Balancers

Benefits include:

- Prevent server overload
- Improve throughput
- Increase availability
- Support rolling deployments
- Enable autoscaling

---

## Load Balancing Algorithms

Examples:

- Round Robin
- Least Connections
- Weighted Round Robin
- IP Hash
- Consistent Hashing

Algorithm selection depends on workload characteristics.

---

# 12.5 Caching

One of the highest ROI scalability techniques.

Instead of repeatedly querying databases:

```
Request

↓

Cache

↓

Database
```

---

## Cache Hit

```
Request

↓

Redis

↓

Response
```

Database is bypassed.

---

## Cache Miss

```
Request

↓

Redis

↓

Database

↓

Update Cache

↓

Response
```

---

## Business Benefits

- Lower latency
- Higher throughput
- Reduced database load
- Lower infrastructure cost

---

## Trade-offs

- Cache invalidation
- Stale data
- Memory usage

---

# Cache Placement

Architects choose cache placement based on workload.

```
Browser Cache

↓

CDN

↓

API Cache

↓

Database Cache
```

Each level removes load from the next.

---

# 12.6 Content Delivery Network (CDN)

A CDN places content closer to users.

Without CDN:

```
India

↓

US Server
```

With CDN:

```
India

↓

Mumbai Edge

↓

Response
```

---

## Best Content for CDN

- Images
- CSS
- JavaScript
- Videos
- Downloads
- Documentation

---

## Benefits

- Lower latency
- Reduced origin traffic
- Better user experience
- Global scalability

---

# 12.7 Database Replication

Reading often becomes the first database bottleneck.

Architects separate reads from writes.

```
Primary

↓

Replica A

Replica B

Replica C
```

---

## Benefits

- Increased read throughput
- Reduced database load
- Better availability

---

## Challenges

- Replication lag
- Eventual consistency
- Read-after-write issues

---

# 12.8 Database Partitioning

Large databases eventually become difficult to scale.

Partitioning divides data into smaller sections.

Example:

```
Orders

↓

2024

↓

2025

↓

2026
```

---

## Benefits

- Smaller indexes
- Faster queries
- Better maintenance
- Easier archival

---

# Partitioning Strategies

Examples:

- Range
- Hash
- List
- Composite

Selection depends on query patterns.

---

# 12.9 Database Sharding

Sharding distributes data across multiple databases.

```
Users

↓

Shard A

Shard B

Shard C
```

Each shard owns only part of the data.

---

## Example

```
Customer IDs

1-1M

↓

Shard A

1M-2M

↓

Shard B
```

---

## Benefits

- Massive scalability
- Parallel processing
- Reduced storage pressure

---

## Challenges

- Cross-shard joins
- Rebalancing
- Hot shards
- Operational complexity

---

# Partitioning vs Sharding

| Partitioning | Sharding |
|--------------|----------|
| Usually within one database | Across multiple databases |
| Easier management | Greater scalability |
| Lower complexity | Higher operational effort |

---

# 12.10 CQRS

Command Query Responsibility Segregation separates:

Writes

from

Reads.

```
Commands

↓

Write Database

↓

Events

↓

Read Database

↓

Queries
```

---

## Benefits

- Independent optimization
- Better scalability
- Specialized storage
- Improved performance

---

## Business Example

E-commerce

Write:

```
Place Order
```

Read:

```
Search Products

View Orders

Recommendations
```

Different workloads scale independently.

---

# 12.11 Event-Driven Architecture

Instead of synchronous communication:

```
Order

↓

Payment

↓

Inventory

↓

Notification
```

Architects introduce events.

```
Order

↓

Kafka

↓

Inventory

↓

Analytics

↓

Notification
```

---

## Benefits

- Loose coupling
- Independent scaling
- Better resilience
- Parallel processing

---

## Challenges

- Event ordering
- Eventual consistency
- Monitoring
- Debugging

---

# 12.12 Asynchronous Processing

Not every request requires immediate processing.

Instead of:

```
User

↓

Long Operation

↓

Response
```

Use:

```
User

↓

Queue

↓

Immediate Response

↓

Background Worker
```

---

## Typical Examples

- Email
- SMS
- Image Processing
- PDF Generation
- AI Inference
- Video Encoding

---

## Benefits

- Faster response time
- Better throughput
- Independent scaling
- Improved user experience

---

# 12.13 Bulk Processing

Instead of processing one item at a time:

```
1000 Requests

↓

1000 Database Writes
```

Architects batch work.

```
1000 Requests

↓

Batch

↓

1 Database Write
```

---

## Benefits

- Lower network overhead
- Better database efficiency
- Higher throughput

---

# 12.14 Parallel Processing

Independent work should execute concurrently.

Instead of:

```
A

↓

B

↓

C
```

Use:

```
A

↓

B      C

↓

Merge
```

---

## Benefits

- Better CPU utilization
- Lower latency
- Higher throughput

---

# 12.15 Rate Limiting

Scalable systems protect themselves.

```
100,000 Requests

↓

Rate Limiter

↓

20,000 Accepted

↓

80,000 Delayed
```

---

## Benefits

- Prevent overload
- Fair resource sharing
- Protect downstream services
- Improve stability

---

# 12.16 Autoscaling

Infrastructure should grow with demand.

```
Traffic ↑

↓

CPU 70%

↓

Add Instances

↓

Traffic Falls

↓

Remove Instances
```

---

## Metrics Used

- CPU
- Memory
- Queue Depth
- Request Rate
- Custom Business Metrics

---

## Benefits

- Lower operational effort
- Better cost optimization
- Faster response to demand

---

# 12.17 Backpressure

Consumers sometimes process requests slower than producers generate them.

Without control:

```
Producer

↓

Queue

↓

Consumer

↓

Overload
```

Backpressure slows producers.

---

## Benefits

- Prevent queue explosion
- Protect downstream services
- Improve system stability

---

# Architecture Decision Matrix

| Decision | Business Problem Solved | Primary Benefit |
|----------|-------------------------|-----------------|
| Stateless Services | Session bottlenecks | Horizontal scaling |
| Horizontal Scaling | Traffic growth | Capacity expansion |
| Vertical Scaling | Immediate capacity | Simplicity |
| Load Balancing | Uneven traffic | Resource utilization |
| Caching | Database overload | Lower latency |
| CDN | Global latency | Faster content delivery |
| Database Replication | Read bottlenecks | Higher throughput |
| Partitioning | Large datasets | Query performance |
| Sharding | Database limits | Massive scale |
| CQRS | Mixed workloads | Independent optimization |
| Event-Driven Architecture | Tight coupling | Independent scaling |
| Asynchronous Processing | Long-running tasks | Better responsiveness |
| Bulk Processing | High write overhead | Efficient resource usage |
| Parallel Processing | Sequential bottlenecks | Higher throughput |
| Rate Limiting | Traffic spikes | Stability |
| Autoscaling | Dynamic demand | Elastic growth |
| Backpressure | Consumer overload | System protection |

---

# Architect's Perspective

Scalability is rarely achieved through a single architectural decision.

Instead, architects combine multiple complementary patterns.

For example, a modern e-commerce platform might use:

- Stateless APIs behind load balancers
- Redis for caching
- Kafka for asynchronous communication
- Database replication for read scaling
- Sharding for massive datasets
- CDN for static content
- Autoscaling for infrastructure elasticity
- Rate limiting to protect critical services

Each decision addresses a different scalability bottleneck.

The objective is not to maximize every scaling technique, but to apply the right combination based on business growth, workload characteristics, and operational maturity.

---

**End of Part 4**

The next section will cover **Mechanisms**, including implementation using relational databases, Redis, Kafka, RabbitMQ, Spring Boot, Kubernetes, AWS, Azure, GCP, autoscaling services, distributed caches, and a comprehensive scalability decision matrix.
