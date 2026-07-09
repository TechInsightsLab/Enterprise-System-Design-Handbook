
# 105. Cloud Database Services

> **Modern cloud platforms provide fully managed database services, allowing architects to focus on business logic instead of infrastructure management.**

Instead of managing:

- Servers
- Storage
- Replication
- Backups
- Failover
- Operating Systems

Cloud providers automate these tasks.

---

# Why Managed Databases?

Traditional database management requires:

```text
Install Database

↓

Configure Replication

↓

Setup Backup

↓

Patch OS

↓

Monitor Health

↓

Handle Failures
```

Managed databases automate most of these operational responsibilities.

---

# Benefits

- Automatic backups
- Automatic failover
- Monitoring
- Scaling
- Security integration
- High Availability
- Reduced operational overhead

---

# Cloud Database Categories

Every cloud provider offers databases for different workloads.

```text
Relational

↓

NoSQL

↓

Cache

↓

Graph

↓

Time Series

↓

Data Warehouse
```

---

# 106. AWS Database Services

AWS provides one of the largest database portfolios.

---

# Amazon RDS

Managed relational database service.

Supports:

- PostgreSQL
- MySQL
- MariaDB
- SQL Server
- Oracle

---

## Features

- Automated Backup
- Read Replicas
- Multi-AZ Deployment
- Automatic Patching
- Monitoring
- Point-in-Time Recovery

---

## Best Use Cases

- ERP
- CRM
- E-Commerce
- Financial Systems

---

# Amazon Aurora

Aurora is AWS's cloud-native relational database.

Compatible with:

- PostgreSQL
- MySQL

---

## Advantages

- Higher throughput
- Faster failover
- Distributed storage
- Automatic replication
- High availability

---

## Architecture

```text
Application

↓

Aurora Writer

↓

Distributed Storage

↓

Aurora Readers
```

Storage replicated across multiple Availability Zones.

---

# Amazon DynamoDB

Fully managed NoSQL Key-Value and Document database.

---

## Features

- Serverless
- Auto Scaling
- Single-digit millisecond latency
- Global Tables
- TTL
- Streams

---

## Best Use Cases

- Shopping Cart
- User Sessions
- Gaming
- IoT
- Metadata

---

# Amazon ElastiCache

Managed caching service.

Supports:

- Redis
- Memcached

---

## Uses

- Session Storage
- Leaderboards
- Rate Limiting
- API Caching

---

# Amazon Neptune

Managed graph database.

Suitable for:

- Fraud Detection
- Knowledge Graphs
- Recommendation Systems

---

# Amazon Timestream

Managed time-series database.

Designed for:

- IoT
- Monitoring
- Metrics
- Telemetry

---

# Amazon Redshift

Cloud Data Warehouse.

Optimized for:

- Analytics
- Reporting
- Business Intelligence

---

# AWS Database Summary

| Service | Category | Typical Use |
|----------|----------|-------------|
| Aurora | Relational | Enterprise OLTP |
| RDS | Relational | General OLTP |
| DynamoDB | NoSQL | Large-scale distributed applications |
| ElastiCache | Cache | Low-latency access |
| Neptune | Graph | Relationship queries |
| Timestream | Time-Series | Monitoring & IoT |
| Redshift | Data Warehouse | Analytics |

---

# 107. Azure Database Services

Microsoft Azure provides a comprehensive managed data platform.

---

# Azure SQL Database

Managed SQL Server.

---

## Features

- Automatic Backup
- Geo Replication
- High Availability
- Scaling
- Security

---

# Azure Database for PostgreSQL

Managed PostgreSQL.

Suitable for:

- Enterprise applications
- SaaS
- Analytics

---

# Azure Database for MySQL

Managed MySQL.

Ideal for:

- Web Applications
- CMS
- E-Commerce

---

# Azure Cosmos DB

Globally distributed NoSQL database.

Supports multiple APIs including:

- SQL
- MongoDB
- Cassandra
- Gremlin
- Table

---

## Features

- Global Replication
- Tunable Consistency Levels
- Multi-Master
- Low Latency
- Elastic Scale

---

# Azure Cache for Redis

Managed Redis.

Uses:

- Caching
- Sessions
- Pub/Sub
- Distributed Locks

---

# Azure Synapse Analytics

Enterprise analytics platform.

Supports:

- Data Warehouse
- Big Data
- Business Intelligence

---

# Azure Database Summary

| Service | Category |
|----------|----------|
| Azure SQL | Relational |
| PostgreSQL | Relational |
| MySQL | Relational |
| Cosmos DB | NoSQL |
| Redis Cache | Cache |
| Synapse | Analytics |

---

# 108. Google Cloud Database Services

Google Cloud emphasizes globally distributed databases.

---

# Cloud SQL

Managed:

- PostgreSQL
- MySQL
- SQL Server

---

# Cloud Spanner

One of Google's flagship database services.

---

## Unique Features

- Global distribution
- Strong consistency
- Horizontal scaling
- SQL support
- Automatic sharding

---

## Best Use Cases

- Banking
- Financial Trading
- Global SaaS
- Enterprise ERP

---

# Firestore

Document database.

Popular for:

- Mobile
- Web Applications
- Firebase

---

# Bigtable

Column-family database.

Optimized for:

- Massive throughput
- Large datasets
- Time-series

---

# Memorystore

Managed Redis and Memcached.

---

# BigQuery

Serverless Data Warehouse.

Excellent for:

- Interactive analytics
- Machine Learning
- Reporting

---

# Google Cloud Summary

| Service | Category |
|----------|----------|
| Cloud SQL | Relational |
| Cloud Spanner | Globally Distributed SQL |
| Firestore | Document |
| Bigtable | Column Family |
| Memorystore | Cache |
| BigQuery | Analytics |

---

# 109. Database Selection Matrix

Choosing a database is one of the most important architectural decisions.

Start with business requirements—not technology preferences.

---

# Decision Matrix

| Requirement | Recommended Database |
|-------------|----------------------|
| Banking | PostgreSQL / Aurora / Cloud Spanner |
| E-Commerce Orders | PostgreSQL / Aurora |
| Product Catalog | MongoDB / Firestore |
| User Sessions | Redis |
| Shopping Cart | DynamoDB / Redis |
| Social Feed | Cassandra / DynamoDB |
| Fraud Detection | Neo4j / Neptune |
| Monitoring | InfluxDB / Timestream |
| Analytics | Redshift / BigQuery / Synapse |
| Object Storage | S3 / Azure Blob / Cloud Storage |

---

# Architecture Decision Questions

Before selecting a database,

ask:

### Data Volume

Gigabytes?

Terabytes?

Petabytes?

---

### Read/Write Ratio

Mostly:

Reads?

Writes?

Balanced?

---

### Latency

Milliseconds?

Microseconds?

Seconds?

---

### Consistency

Strong?

Eventual?

Session?

---

### Availability

How much downtime is acceptable?

---

### Scalability

Vertical?

Horizontal?

Global?

---

### Query Pattern

Primary access by:

- Primary Key?
- Search?
- Graph traversal?
- Analytics?
- Time range?

---

### Operational Complexity

Can the organization operate:

- Multiple databases?
- Distributed clusters?
- Global replication?

---

# Database Decision Flow

```text
Need Transactions?

↓

Yes

↓

Relational Database

↓

No

↓

Flexible Schema?

↓

Yes

↓

Document Database

↓

No

↓

Key Lookup?

↓

Yes

↓

Key-Value Store

↓

No

↓

Relationship Queries?

↓

Graph Database

↓

Time-Based Data?

↓

Time-Series Database

↓

Analytics?

↓

Data Warehouse
```

---

# Polyglot Persistence Example

Large e-commerce platform.

```text
Orders

↓

Aurora

-------------------

Catalog

↓

MongoDB

-------------------

Cache

↓

Redis

-------------------

Analytics

↓

Redshift

-------------------

Search

↓

Elasticsearch

-------------------

Images

↓

S3
```

Each database serves a distinct purpose.

---

# 110. Enterprise Case Studies

## Amazon

### Problem

Millions of concurrent customers.

Petabytes of product data.

---

### Solution

- DynamoDB
- Aurora
- S3
- Redshift
- ElastiCache

Each service optimized for its workload.

---

### Lessons

Use specialized storage technologies rather than forcing every workload into a single database.

---

# Google

### Challenge

Global-scale applications.

Low latency.

Strong consistency.

---

### Solution

Cloud Spanner.

Globally distributed SQL with strong consistency.

---

### Lessons

Global scale requires careful trade-offs between latency, consistency, and availability.

---

# Netflix

### Challenge

Millions of simultaneous streams.

Huge personalization datasets.

---

### Solution

Distributed data architecture with:

- Cassandra
- Elasticsearch
- Redis
- S3

Combined with event-driven systems.

---

### Lessons

Separate operational workloads from search, caching, and analytics.

---

# Uber

### Challenge

Real-time driver location.

Ride matching.

Payments.

---

### Solution

Different databases for:

- Transactions
- Geospatial indexing
- Analytics
- Event streaming

---

### Lessons

Different business capabilities often require different storage engines.

---

# LinkedIn

### Challenge

Professional graph.

Feed generation.

Messaging.

---

### Solution

Polyglot persistence.

Heavy use of:

- Kafka
- Search
- Graph technologies
- Distributed storage

---

### Lessons

Data architecture evolves with business capabilities.

---

# Enterprise Comparison

| Company | Data Strategy |
|----------|---------------|
| Amazon | Polyglot Persistence |
| Google | Globally Distributed SQL |
| Netflix | Distributed Specialized Storage |
| Uber | Domain-Oriented Storage |
| LinkedIn | Event-Driven Data Platform |

---

# 111. Production Incidents

## Incident 1

### Database Hot Partition

Orders sharded by:

```
Country
```

Black Friday.

Most traffic:

United States.

One shard overloaded.

---

### Root Cause

Poor sharding key.

---

### Solution

Hash-based distribution.

---

# Incident 2

### Missing Backup Validation

Backups existed.

Restore failed.

Backup corrupted.

---

### Root Cause

Recovery never tested.

---

### Solution

Monthly disaster recovery drills.

---

# Incident 3

### Replication Lag

Application read from replica immediately after write.

Customer viewed:

Old data.

---

### Root Cause

Asynchronous replication.

---

### Solution

Use primary for read-after-write workloads or implement consistency-aware routing.

---

# Incident 4

### Schema Migration Failure

Column renamed.

Older application version still expected old column.

Production outage.

---

### Root Cause

Breaking schema change.

---

### Solution

Backward-compatible migrations with phased deployments.

---

# Lessons Learned

| Incident | Lesson |
|----------|---------|
| Hot Partition | Choose shard keys carefully |
| Backup Failure | Test restores regularly |
| Replica Lag | Understand consistency guarantees |
| Migration Failure | Evolve schemas safely |

---

# 112. Architecture Review Checklist

## Data Modeling

- [ ] Business entities identified?
- [ ] Ownership defined?
- [ ] Aggregate boundaries clear?
- [ ] Normalization appropriate?

---

## Database Selection

- [ ] Technology matches workload?
- [ ] Scalability considered?
- [ ] Operational complexity acceptable?
- [ ] Polyglot persistence justified?

---

## Distributed Data

- [ ] Partition strategy defined?
- [ ] Sharding key validated?
- [ ] Replication configured?
- [ ] Multi-region requirements evaluated?

---

## Reliability

- [ ] Backup strategy documented?
- [ ] PITR enabled?
- [ ] Disaster recovery tested?
- [ ] RPO/RTO agreed with business?

---

## Security

- [ ] Encryption at rest?
- [ ] Encryption in transit?
- [ ] Secrets externalized?
- [ ] Audit logging enabled?

---

## Governance

- [ ] Retention policy defined?
- [ ] Compliance requirements identified?
- [ ] Data classification complete?
- [ ] Data ownership assigned?

---

# 113. Architecture Decision Record (ADR)

## ADR-010

### Title

Adopt Polyglot Persistence for the E-Commerce Platform

---

### Context

The platform supports:

- Orders
- Product catalog
- Customer sessions
- Search
- Analytics
- Media assets

Each workload has distinct performance and scalability requirements.

---

### Decision

Use:

- Aurora PostgreSQL for transactional data
- Redis for caching and sessions
- Elasticsearch for search
- S3 for object storage
- Redshift for analytics

---

### Alternatives Considered

1. Single PostgreSQL database
2. PostgreSQL + Redis only
3. Fully NoSQL architecture

---

### Rationale

A polyglot approach aligns storage technology with workload characteristics, improving scalability and operational efficiency while maintaining strong transactional guarantees where required.

---

### Consequences

Positive:

- Better performance
- Independent scaling
- Specialized capabilities

Negative:

- Increased operational complexity
- More monitoring
- Cross-system integration challenges

---

### Success Criteria

- P99 query latency < 100 ms for OLTP
- Search latency < 300 ms
- Analytics isolated from production
- Zero data loss within agreed RPO
- Successful quarterly disaster recovery exercises

---

**End of Part 11**

The next section will complete the chapter with:

- **Interview Questions (Beginner → Principal Architect)**
- **One-Page Revision Cheat Sheet**
- **Architect's Golden Principles**
- **Chapter Completion Checklist**
- **Comprehensive Chapter Summary**
- **Connection to Previous Chapters**
- **Transition to Chapter 11**
