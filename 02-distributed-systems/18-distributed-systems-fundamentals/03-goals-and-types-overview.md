
# 14. Goals of Distributed Systems

A distributed system is **not** built simply because multiple servers are available.

Organizations adopt distributed systems.

To achieve.

Specific business goals.

Every architectural decision.

Should support.

One or more.

Of these goals.

---

# Primary Goals

Modern distributed systems aim to achieve.

- Resource Sharing
- Scalability
- High Availability
- Reliability
- Fault Tolerance
- Performance
- Geographic Distribution
- Transparency
- Flexibility
- Cost Efficiency

These goals.

Often compete.

With each other.

Architects must balance them.

---

# Goal 1 — Resource Sharing

> **Allow multiple users, services, and applications to efficiently share computing resources.**

Resources include.

- CPU
- Memory
- Storage
- Databases
- Message Queues
- Files
- APIs

Instead of duplicating resources.

Multiple systems.

Share them.

---

# Example

Without resource sharing.

```text
Application A

↓

Own Database

-------------------

Application B

↓

Own Database

-------------------

Application C

↓

Own Database
```

Expensive.

Difficult to maintain.

---

With resource sharing.

```text
Application A

↓

Shared Database

↑

Application B

↑

Application C
```

Resources.

Are utilized.

More efficiently.

---

# Benefits

- Lower cost
- Better utilization
- Easier collaboration
- Simplified operations

---

# Trade-Off

Shared resources.

May become.

Performance bottlenecks.

Or single points of failure.

---

# Goal 2 — Scalability

Distributed systems.

Allow capacity.

To grow.

Incrementally.

---

Example.

Traffic increases.

```text
1,000 Users

↓

10,000 Users

↓

1 Million Users
```

Instead of replacing.

One server.

We add.

More servers.

---

# Architecture

```text
Users

↓

Load Balancer

↓

App 1

↓

App 2

↓

App 3
```

Each server.

Processes.

Part of the workload.

---

# Benefits

- Handles business growth
- Supports peak traffic
- Improves throughput

---

# Architect's Perspective

Scalability is often.

The first reason.

Organizations move.

To distributed systems.

---

# Goal 3 — High Availability

Users expect.

Applications.

To remain available.

24×7.

---

Example.

One server fails.

```text
Server A

❌

Server B

✓

Server C

✓
```

Traffic.

Automatically shifts.

To healthy servers.

---

# Benefits

- Reduced downtime
- Better customer trust
- SLA compliance

---

# Goal 4 — Reliability

Distributed systems.

Should continue.

Producing.

Correct results.

Even.

When components fail.

---

Example.

Customer.

Transfers money.

The transaction.

Must not disappear.

Even if.

One service crashes.

---

Reliability focuses on.

Correctness.

Not just availability.

---

# Goal 5 — Fault Tolerance

Failures are inevitable.

Distributed systems.

Must continue operating.

Despite failures.

---

Example.

```text
Kafka Broker

↓

Crash

↓

Other Brokers Continue
```

Business continues.

Without interruption.

---

Fault tolerance.

Reduces business impact.

During failures.

---

# Goal 6 — Performance

Users expect.

Fast responses.

Regardless of.

System size.

---

Distributed systems improve performance.

Through.

- Parallel processing
- Caching
- Read replicas
- Data locality
- CDNs

---

Example.

Instead of.

One database.

Serving.

Every request.

Multiple replicas.

Serve reads.

Reducing latency.

---

# Goal 7 — Geographic Distribution

Businesses.

Serve customers.

Worldwide.

Applications should.

Run closer.

To users.

---

Example.

```text
India

↓

Mumbai Region

----------------------

Europe

↓

Frankfurt Region

----------------------

US

↓

Virginia Region
```

Requests.

Travel shorter distances.

Reducing latency.

---

# Benefits

- Better user experience
- Disaster recovery
- Regulatory compliance
- Lower latency

---

# Goal 8 — Transparency

Users.

Should not know.

That the application.

Runs across.

Hundreds of machines.

---

Example.

Customer accesses.

```text
https://shop.example.com
```

The user does not know.

Whether the request.

Was processed.

In Mumbai.

Singapore.

Or Frankfurt.

---

Distributed complexity.

Should remain hidden.

---

# Goal 9 — Flexibility

Distributed systems.

Allow components.

To evolve independently.

---

Example.

Recommendation Service.

Needs AI.

Inventory Service.

Does not.

Each team.

Deploys independently.

---

Benefits.

- Faster innovation
- Independent releases
- Easier upgrades

---

# Goal 10 — Cost Optimization

Instead of.

Buying.

One huge server.

Organizations use.

Many commodity servers.

---

Example.

```text
One High-End Server

↓

$100,000

-----------------------

Ten Smaller Servers

↓

$40,000
```

Horizontal scaling.

Often reduces.

Infrastructure cost.

---

Cloud platforms.

Further improve.

Cost efficiency.

Using.

Autoscaling.

---

# Balancing Goals

Unfortunately.

These goals.

Cannot all.

Be maximized.

Simultaneously.

---

Example.

Improve availability.

```text
Replication

↓

Higher Cost

↓

More Coordination
```

---

Improve consistency.

```text
Synchronous Replication

↓

Higher Latency
```

---

Improve throughput.

```text
Batch Processing

↓

Higher Response Time
```

Architects.

Continuously balance.

Competing objectives.

---

# Business Drivers for Distributed Systems

Why do companies.

Actually adopt.

Distributed systems?

---

# Driver 1 — Business Growth

Startup.

```text
100 Users
```

Enterprise.

```text
100 Million Users
```

Growth requires.

Distributed architecture.

---

# Driver 2 — Global Expansion

Businesses expand.

Across continents.

Applications must.

Run closer.

To customers.

---

# Driver 3 — Always-On Services

Modern businesses.

Cannot afford.

Long outages.

Distributed systems.

Improve availability.

---

# Driver 4 — Massive Data

Data grows.

From.

Gigabytes.

↓

Terabytes.

↓

Petabytes.

↓

Exabytes.

One machine.

Cannot store.

Everything efficiently.

---

# Driver 5 — Faster Innovation

Independent teams.

Deploy independently.

Without coordinating.

Entire releases.

---

# Driver 6 — Cloud Adoption

Cloud providers.

Encourage.

Distributed architectures.

Using.

Containers.

Microservices.

Managed services.

Autoscaling.

---

# Real-World Example

Netflix.

Does not run.

On one server.

Instead.

Thousands of services.

Collaborate.

Across multiple regions.

Providing.

One streaming platform.

---

# Another Example

Amazon.

Order Placement.

Touches.

```text
Cart

↓

Inventory

↓

Payment

↓

Shipping

↓

Recommendations

↓

Notifications
```

Many distributed systems.

Appear.

As one website.

---

# Centralized vs Distributed Goals

| Centralized System | Distributed System |
|--------------------|-------------------|
| Simplicity | Scalability |
| Single Machine | Many Machines |
| Easier Development | Better Availability |
| Lower Coordination | Better Fault Isolation |
| Limited Growth | Massive Growth |

---

# Common Misconceptions

### Misconception 1

"We need microservices."

Question first.

Do you need.

Independent scalability?

Independent deployment?

Independent teams?

If not.

A monolith.

May be sufficient.

---

### Misconception 2

"Distributed systems are always faster."

False.

Network communication.

Adds latency.

Distributed systems.

Trade simplicity.

For scalability.

---

### Misconception 3

"Adding more servers solves everything."

More servers introduce.

- More communication.
- More failures.
- More coordination.
- More monitoring.

Scale adds complexity.

---

# Architect's Perspective

Distributed systems are not an end goal.

They are a means to achieve business outcomes.

Before distributing a system, architects should ask:

- What business problem are we solving?
- Is the current architecture truly insufficient?
- Which quality attribute needs improvement?
- Is the additional complexity justified?

The best architecture is not the most distributed one.

It is the **simplest architecture that satisfies both current and foreseeable business requirements**.

---

# 15. Types of Distributed Systems (Overview)

Not all distributed systems.

Solve the same problem.

Different systems.

Have different goals.

Broadly.

Distributed systems can be categorized into:

| Type | Primary Purpose | Examples |
|------|-----------------|----------|
| Distributed Computing Systems | Share computation across multiple machines | Kubernetes, Hadoop, Spark |
| Distributed Information Systems | Share business data and transactions | Banking Systems, ERP, Microservices |
| Distributed Storage Systems | Store and manage massive datasets | HDFS, Cassandra, DynamoDB, S3 |
| Distributed Database Systems | Provide scalable and reliable databases | CockroachDB, Google Spanner, YugabyteDB |
| Distributed File Systems | Share files across multiple machines | HDFS, NFS, GFS |
| Distributed Messaging Systems | Reliable communication between services | Kafka, RabbitMQ, Pulsar |
| Peer-to-Peer (P2P) Systems | Decentralized communication without central servers | BitTorrent, Blockchain Networks |
| Content Distribution Systems | Deliver content closer to users | CloudFront, Akamai, Cloudflare CDN |

> Each category addresses different business and technical challenges. The following sections will explore these types in detail.

---

**End of Part 3**

The next part will cover:

- **Distributed Computing Systems**
- **Distributed Information Systems**
- **Distributed Storage Systems**
- **Distributed Database Systems**
- **Distributed File Systems**
- **Messaging Systems**
- **Peer-to-Peer Systems**
- **Content Distribution Systems**
- **Real-World Examples**
