
# 16. Types of Distributed Systems

Distributed systems.

Are not all the same.

Some are designed.

To process data.

Others.

Store data.

Some coordinate services.

Others deliver content globally.

Understanding these categories.

Helps architects.

Choose the right technology.

For the right problem.

---

# Classification

Distributed systems are commonly classified as:

```text
Distributed Systems

│

├── Distributed Computing Systems

├── Distributed Information Systems

├── Distributed Storage Systems

├── Distributed Database Systems

├── Distributed File Systems

├── Distributed Messaging Systems

├── Peer-to-Peer Systems

└── Content Distribution Systems
```

Each category.

Solves different problems.

---

# 16.1 Distributed Computing Systems

> **Distributed Computing Systems divide computational work across multiple machines to solve problems faster and more efficiently.**

Instead of.

One machine.

Doing all computation.

Many machines.

Process work.

Simultaneously.

---

# Why Needed?

Imagine.

Processing.

100 TB.

Of log files.

One machine.

May require.

Several days.

---

Distributed Computing.

```text
100 Machines

↓

Each Processes

1 TB

↓

Results Combined
```

Processing finishes.

Much faster.

---

# Architecture

```text
                Master

                  │

      ┌───────────┼───────────┐

      │           │           │

 Worker 1     Worker 2     Worker 3

      │           │           │

 Process      Process      Process

      │           │           │

      └───────────┼───────────┘

             Final Result
```

---

# Characteristics

- Parallel execution
- Task scheduling
- Fault recovery
- Load balancing
- High throughput

---

# Real-World Examples

Examples.

- Apache Spark
- Hadoop MapReduce
- Kubernetes Jobs
- Ray
- Apache Flink

---

# Business Use Cases

- Big Data Analytics
- Machine Learning
- Scientific Computing
- Video Rendering
- Image Processing
- Batch Processing

---

# Example

Netflix.

Analyzes billions.

Of viewing events.

Using distributed computing.

Not one server.

---

# Architect's Perspective

Use distributed computing.

When computation.

Becomes the bottleneck.

Not storage.

---

# 16.2 Distributed Information Systems

> **Distributed Information Systems enable multiple applications and services to collaborate while sharing business information.**

Most enterprise applications.

Belong.

To this category.

---

# Example

Online Shopping.

```text
Customer

↓

Order Service

↓

Inventory Service

↓

Payment Service

↓

Shipping Service
```

Every service.

Maintains.

Its own logic.

Yet collaborates.

With others.

---

# Characteristics

- Business transactions
- APIs
- Messaging
- Event-driven communication
- Independent services

---

# Technologies

Examples.

- REST APIs
- gRPC
- Kafka
- RabbitMQ
- Service Mesh

---

# Business Examples

- Banking
- ERP
- CRM
- Insurance
- E-commerce

---

# Architect's Perspective

Microservices.

Are a form.

Of distributed information system.

---

# 16.3 Distributed Storage Systems

> **Distributed Storage Systems store massive amounts of data across multiple machines while providing durability, scalability, and availability.**

Storage grows.

Beyond.

One machine.

Data is distributed.

Across many servers.

---

# Architecture

```text
Application

↓

Storage Cluster

↓

Node A

↓

Node B

↓

Node C
```

Files.

Objects.

Blocks.

May be distributed.

Across nodes.

---

# Characteristics

- Data replication
- Data partitioning
- High durability
- Fault tolerance
- Massive storage

---

# Examples

- Amazon S3
- Azure Blob Storage
- Google Cloud Storage
- Ceph
- MinIO

---

# Business Uses

- Images
- Videos
- Documents
- Backups
- Archives
- Data Lakes

---

# Example

YouTube.

Stores.

Millions.

Of videos.

Using distributed storage.

---

# Architect's Perspective

Distributed storage.

Optimizes.

Capacity.

Durability.

Availability.

Not complex querying.

---

# 16.4 Distributed Database Systems

> **Distributed Database Systems provide a single logical database while storing data across multiple physical machines.**

Unlike storage systems.

Distributed databases.

Support.

Queries.

Transactions.

Indexes.

Replication.

---

# Architecture

```text
Application

↓

Database Cluster

↓

Node A

↓

Node B

↓

Node C
```

Applications.

See.

One database.

Internally.

Data is distributed.

---

# Characteristics

- SQL or NoSQL
- Replication
- Partitioning
- Transactions
- Automatic failover

---

# Examples

SQL

- Google Spanner
- CockroachDB
- YugabyteDB

NoSQL

- Cassandra
- DynamoDB
- MongoDB Cluster

---

# Business Uses

- Financial systems
- User profiles
- Inventory
- Orders
- Payments

---

# Storage vs Database

Many beginners.

Confuse these.

| Storage System | Database System |
|---------------|-----------------|
| Stores files and objects | Stores structured data |
| Optimized for durability | Optimized for querying |
| No SQL | SQL or NoSQL |
| Large binary objects | Business records |

---

# Architect's Perspective

Need SQL?

Need joins?

Need transactions?

Choose.

Distributed Database.

Need object storage?

Choose.

Distributed Storage.

---

# 16.5 Distributed File Systems

> **A Distributed File System (DFS) allows files to be stored across multiple machines while appearing as a single file system.**

Applications.

Access files.

Without knowing.

Where they physically reside.

---

# Example

```text
Application

↓

Distributed File System

↓

Server A

↓

Server B

↓

Server C
```

Files.

May be split.

Replicated.

Across servers.

---

# Characteristics

- File abstraction
- Replication
- Fault tolerance
- Large file support
- High throughput

---

# Examples

- HDFS
- Google File System (GFS)
- Amazon EFS
- NFS
- CephFS

---

# Business Uses

- Big Data
- Analytics
- AI Training
- Shared File Storage

---

# Architect's Perspective

Distributed file systems.

Optimize.

File access.

Not transactional workloads.

---

# 16.6 Distributed Messaging Systems

> **Distributed Messaging Systems provide reliable communication between distributed components through asynchronous message exchange.**

Instead of.

Calling services directly.

Applications exchange.

Messages.

---

# Architecture

```text
Producer

↓

Broker

↓

Consumer
```

The producer.

Does not wait.

For processing.

---

# Characteristics

- Decoupling
- Reliability
- Ordering
- Replay
- Scalability

---

# Examples

- Kafka
- RabbitMQ
- Pulsar
- Amazon SQS
- Azure Service Bus

---

# Business Uses

- Event Processing
- Notifications
- Payments
- Audit Logs
- Data Pipelines

---

# Architect's Perspective

Messaging systems.

Improve resilience.

And throughput.

At the cost of.

Eventual consistency.

---

# 16.7 Peer-to-Peer Systems (P2P)

> **Peer-to-Peer systems distribute responsibility among participating nodes without relying on a single centralized server.**

Every node.

Can act.

As both.

Client.

And server.

---

# Architecture

```text
Peer A

↔

Peer B

↔

Peer C

↔

Peer D
```

No central coordinator.

Required.

---

# Characteristics

- Decentralized
- Self-organizing
- Fault tolerant
- Scalable

---

# Examples

- BitTorrent
- Blockchain
- Bitcoin
- Ethereum
- IPFS

---

# Benefits

- No single point of failure
- Highly decentralized
- Community resource sharing

---

# Challenges

- Trust
- Security
- Consensus
- Performance

---

# Architect's Perspective

P2P systems.

Trade centralized control.

For decentralization.

---

# 16.8 Content Distribution Systems

> **Content Distribution Systems deliver content from locations geographically closer to users.**

Their goal.

Is low latency.

---

# Architecture

```text
User

↓

Nearest Edge Server

↓

Origin Server
```

Most requests.

Never reach.

The origin.

---

# Examples

- Cloudflare
- Amazon CloudFront
- Akamai
- Azure CDN

---

# Characteristics

- Edge caching
- Geographic distribution
- Low latency
- High throughput

---

# Business Uses

- Websites
- Video Streaming
- Images
- Software Downloads
- Gaming

---

# Example

Netflix.

Serves videos.

From edge servers.

Near users.

Instead of.

Streaming everything.

From one data center.

---

# Comparing the Types

| Type | Primary Goal | Typical Examples |
|------|--------------|------------------|
| Distributed Computing | Parallel computation | Spark, Hadoop |
| Distributed Information | Business workflows | Microservices, Banking |
| Distributed Storage | Massive object storage | S3, MinIO |
| Distributed Database | Scalable querying | Spanner, Cassandra |
| Distributed File System | Shared files | HDFS, GFS |
| Distributed Messaging | Asynchronous communication | Kafka, RabbitMQ |
| Peer-to-Peer | Decentralization | Bitcoin, BitTorrent |
| Content Distribution | Low-latency delivery | CloudFront, Cloudflare |

---

# Which Type Should You Choose?

Ask yourself.

**What problem am I solving?**

| Problem | Suitable System |
|----------|-----------------|
| Massive computation | Distributed Computing |
| Business transactions | Distributed Information |
| Store billions of images | Distributed Storage |
| Scalable database | Distributed Database |
| Shared enterprise files | Distributed File System |
| Asynchronous processing | Distributed Messaging |
| Decentralized trust | Peer-to-Peer |
| Global content delivery | CDN |

The technology should follow.

The problem.

Not the other way around.

---

# Common Misconceptions

### Misconception 1

> Kafka is a distributed database.

False.

Kafka.

Is a distributed messaging system.

---

### Misconception 2

> Amazon S3 is a database.

False.

It is.

A distributed object storage system.

---

### Misconception 3

> HDFS is a database.

False.

HDFS.

Is a distributed file system.

---

### Misconception 4

> Kubernetes stores data.

False.

Kubernetes.

Is primarily a distributed orchestration platform.

It manages workloads.

Not persistent business data.

---

# Architect's Perspective

Understanding these categories prevents one of the most common architectural mistakes:

**Using the right technology to solve the wrong problem.**

For example:

- Don't use object storage as a database.
- Don't use Kafka as a request-response API.
- Don't use a distributed database for large media files.
- Don't use a CDN to coordinate transactions.

An experienced architect first identifies the nature of the problem—computation, storage, messaging, querying, or content delivery—and then selects the appropriate class of distributed system.

---

**End of Part 4**

The next part will cover:

- **The Fallacies of Distributed Computing**
- **Why Distributed Systems Are Hard**
- **Network Communication Challenges**
- **Partial Failures**
- **Latency**
- **Bandwidth**
- **Unreliable Networks**
- **Message Loss, Duplication & Reordering**
- **Architectural Implications**
