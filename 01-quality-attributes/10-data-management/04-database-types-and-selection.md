
# 26. Database Types

> **There is no single database that is best for every problem.**

One of the biggest mistakes architects make is trying to solve every problem using one database.

Modern enterprise systems often use **Polyglot Persistence**, meaning multiple database technologies coexist in the same system.

Example:

```text
Customer Service

↓

PostgreSQL

----------------------

Product Catalog

↓

MongoDB

----------------------

Cache

↓

Redis

----------------------

Analytics

↓

ClickHouse

----------------------

Logs

↓

Elasticsearch
```

Each database is chosen because it solves a specific problem better.

---

# Evolution of Databases

```text
File System

↓

Relational Database

↓

Distributed Database

↓

NoSQL

↓

Cloud Databases

↓

Multi-Model Databases
```

As businesses evolved,

database technologies evolved too.

---

# Database Classification

Enterprise databases are broadly categorized as:

```text
Databases

├── Relational (SQL)

├── NoSQL

│   ├── Key-Value

│   ├── Document

│   ├── Column Family

│   └── Graph

├── Time-Series

├── Search Engines

├── Data Warehouse

├── Object Storage

└── Multi-Model
```

Each category has different strengths.

---

# 27. Relational Databases (RDBMS)

> **Relational databases organize data into tables connected through relationships.**

They have been the backbone of enterprise systems for over four decades.

---

# Basic Structure

```text
Customer

↓

Order

↓

Order Item

↓

Product
```

Relationships enforce business integrity.

---

# Characteristics

- Structured schema
- SQL language
- ACID transactions
- Strong consistency
- Referential integrity
- Mature ecosystem

---

# Example

Customer

| Customer ID | Name |
|-------------|------|
| 101 | John |

Order

| Order ID | Customer ID |
|----------|--------------|
| 1001 | 101 |

Foreign Key ensures:

Order always references an existing customer.

---

# Advantages

- Strong consistency
- Powerful querying
- Mature optimization
- Excellent reporting
- Transaction support

---

# Disadvantages

- Schema changes require planning
- Horizontal scaling is harder
- Large joins become expensive
- Less flexible for rapidly evolving data

---

# Common RDBMS

| Database | Strength |
|----------|-----------|
| PostgreSQL | Enterprise features |
| MySQL | Simplicity and popularity |
| Oracle | Large enterprise systems |
| SQL Server | Microsoft ecosystem |
| MariaDB | MySQL-compatible open source |

---

# Best Use Cases

- Banking
- ERP
- HRMS
- CRM
- Inventory
- Payment Systems

Anywhere business correctness is critical.

---

# 28. NoSQL Databases

> **NoSQL databases trade some relational features to improve scalability, flexibility, or performance.**

"NoSQL" means:

```
Not Only SQL
```

Many NoSQL databases support SQL-like queries.

---

# Why NoSQL Was Created

Traditional databases struggled with:

- Massive internet scale
- Flexible schemas
- Petabytes of data
- Horizontal scaling
- Global distribution

Companies like Google, Amazon, Facebook, and LinkedIn required different approaches.

---

# Characteristics

- Flexible schema
- Horizontal scaling
- High availability
- Distributed architecture
- Eventual consistency (many systems, though not all)
- Optimized for specific workloads

---

# NoSQL Families

```text
NoSQL

├── Key-Value

├── Document

├── Column Family

└── Graph
```

Each solves different business problems.

---

# SQL vs NoSQL Philosophy

Relational databases emphasize:

```
Correctness First
```

Many NoSQL systems emphasize:

```
Scalability First
```

Neither approach is universally superior.

---

# 29. Key-Value Databases

> **A Key-Value database stores data as a unique key mapped to a value.**

Simplest database model.

---

# Example

```text
user:101

↓

{
Name: "John"
}
```

Everything retrieved by key.

---

# Analogy

Dictionary.

```text
Word

↓

Meaning
```

Key.

↓

Value.

---

# Advantages

- Extremely fast
- Simple architecture
- Horizontal scaling
- Excellent caching

---

# Disadvantages

- Limited querying
- No joins
- Complex filtering
- Relationships handled by applications

---

# Common Systems

| Database | Typical Use |
|----------|-------------|
| Redis | Cache, sessions, rate limiting |
| DynamoDB | Serverless applications |
| Riak | Distributed storage |
| Aerospike | Low-latency workloads |

---

# Enterprise Use Cases

- User Sessions
- Shopping Cart
- Feature Flags
- Rate Limiting
- Leaderboards
- Token Storage

---

# 30. Document Databases

> **Document databases store self-contained documents, usually in JSON-like formats.**

Instead of rows,

they store complete business objects.

---

# Example

```json
{
  "customerId":101,
  "name":"John",
  "orders":[
      {
         "orderId":1001
      }
  ]
}
```

Entire customer stored together.

---

# Advantages

- Flexible schema
- Nested objects
- Easy evolution
- Good developer productivity

---

# Disadvantages

- Duplicate data possible
- Complex joins limited
- Cross-document transactions vary by implementation

---

# Common Systems

| Database | Typical Use |
|----------|-------------|
| MongoDB | General-purpose document storage |
| Couchbase | Distributed document database |
| CouchDB | Offline-first synchronization |
| Firestore | Mobile and web applications |

---

# Best Use Cases

- Product Catalog
- CMS
- User Profiles
- Configuration
- Content Platforms

---

# 31. Column-Family Databases

> **Column-family databases store data by columns instead of rows and are optimized for massive distributed workloads.**

Designed for:

Huge datasets.

---

# Traditional Table

```text
Row

↓

Column A

Column B

Column C
```

---

# Column Storage

```text
Column A

↓

Values

----------------

Column B

↓

Values
```

Only required columns are read.

---

# Advantages

- Excellent write throughput
- High scalability
- Efficient analytics
- Distributed architecture

---

# Disadvantages

- Data modeling differs significantly from RDBMS
- Limited joins
- Query flexibility reduced

---

# Common Systems

| Database | Typical Use |
|----------|-------------|
| Cassandra | Distributed writes |
| HBase | Hadoop ecosystem |
| ScyllaDB | High-performance Cassandra-compatible database |

---

# Best Use Cases

- IoT
- Time-series workloads
- Logging
- Telemetry
- Messaging
- Large-scale event storage

---

# 32. Graph Databases

> **Graph databases model relationships as first-class citizens.**

Instead of:

Rows.

Think:

Nodes and edges.

---

# Example

```text
John

↓

Friend

↓

Alice

↓

Works At

↓

Company
```

Relationships become data.

---

# Advantages

- Relationship traversal is fast
- Flexible schema
- Complex network queries
- Natural representation

---

# Disadvantages

- Not ideal for transactional workloads
- Horizontal scaling can be challenging depending on the graph database

---

# Common Systems

| Database | Typical Use |
|----------|-------------|
| Neo4j | Knowledge graphs |
| Amazon Neptune | Managed graph database |
| JanusGraph | Large distributed graphs |

---

# Enterprise Use Cases

- Fraud Detection
- Social Networks
- Recommendation Engines
- Identity Relationships
- Supply Chain Networks

---

# 33. Time-Series Databases

Designed specifically for:

Time-based data.

---

# Example

```text
Timestamp

↓

CPU

↓

Memory

↓

Temperature
```

Millions of measurements.

---

# Characteristics

- Timestamp optimized
- Compression
- Fast aggregation
- Retention policies

---

# Common Systems

| Database | Typical Use |
|----------|-------------|
| InfluxDB | Monitoring |
| TimescaleDB | PostgreSQL extension |
| OpenTSDB | Large-scale metrics |

---

# Enterprise Use Cases

- Monitoring
- IoT
- Financial Markets
- Sensor Data
- Application Metrics

---

# 34. Search Engines

Search engines are often mistaken for databases.

Primary goal:

Search.

Not transactional storage.

---

# Capabilities

- Full-text search
- Ranking
- Fuzzy search
- Aggregations

---

# Common Systems

| Engine | Use |
|---------|-----|
| Elasticsearch | Search & analytics |
| OpenSearch | Open-source search platform |
| Solr | Enterprise search |

---

# Example

Searching:

```
wireless headphones
```

Search engine ranks results.

Database simply returns matching rows.

---

# 35. Object Storage

Object storage stores:

Files.

Not relational records.

---

# Examples

- Images
- Videos
- PDFs
- Documents
- Backups

---

# Architecture

```text
Object

↓

Metadata

↓

Unique Identifier
```

---

# Advantages

- Massive scalability
- Low cost
- High durability
- Simple API

---

# Common Services

| Platform | Service |
|----------|---------|
| AWS | S3 |
| Azure | Blob Storage |
| Google Cloud | Cloud Storage |
| MinIO | Self-hosted object storage |

---

# Use Cases

- Image uploads
- Media streaming
- Backup archives
- Static websites
- ML datasets

---

# 36. Polyglot Persistence

> **Polyglot Persistence means using different database technologies for different parts of the same system.**

Example:

```text
Customer

↓

PostgreSQL

----------------------

Session

↓

Redis

----------------------

Logs

↓

Elasticsearch

----------------------

Media

↓

S3

----------------------

Recommendations

↓

Neo4j
```

Each technology solves a specific workload.

---

# Why Polyglot Persistence?

No database optimizes for:

- Transactions
- Search
- Graph traversal
- Caching
- Analytics

simultaneously.

---

# Benefits

- Better performance
- Better scalability
- Technology optimized for workload
- Independent evolution

---

# Challenges

- More operational complexity
- Multiple backup strategies
- Data synchronization
- Team expertise
- Cross-system consistency

---

# SQL vs NoSQL Comparison

| Feature | SQL | NoSQL |
|----------|-----|--------|
| Schema | Fixed | Flexible |
| Transactions | Strong | Varies by database |
| Horizontal Scaling | More challenging | Easier in many systems |
| Joins | Excellent | Limited or application-managed |
| Consistency | Strong by default | Depends on database and configuration |
| Best For | OLTP | Large-scale distributed workloads and flexible schemas |

---

# Choosing the Right Database

Ask the following questions.

### Is strong consistency required?

Examples:

- Banking
- Payments

Prefer:

Relational Database.

---

### Is the schema changing frequently?

Example:

Content Management.

Prefer:

Document Database.

---

### Is ultra-fast lookup required?

Example:

Sessions.

Prefer:

Key-Value Store.

---

### Are relationships the primary concern?

Example:

Fraud Detection.

Prefer:

Graph Database.

---

### Are time-based metrics collected continuously?

Prefer:

Time-Series Database.

---

### Are files being stored?

Prefer:

Object Storage.

---

# Common Mistakes

### One Database for Everything

Trying to use PostgreSQL for:

- Cache
- Search
- Analytics
- Blob Storage

creates unnecessary complexity.

---

### Choosing NoSQL Only for Scalability

Many SQL databases scale extremely well with proper architecture.

Technology choice should follow workload, not trends.

---

### Ignoring Operational Complexity

Adding five database technologies means:

- Five monitoring strategies
- Five backup processes
- Five upgrade paths
- Five operational skill sets

Keep the architecture as simple as possible.

---

# Architect's Perspective

Experienced architects do not ask:

> "Which database is best?"

Instead, they ask:

- What are the access patterns?
- What consistency guarantees are required?
- What is the expected data volume?
- How will the data evolve?
- What are the latency requirements?
- What are the operational constraints?

Only after understanding the workload do they choose the storage technology.

The guiding principle is:

> **Choose the simplest database that satisfies today's business requirements while allowing reasonable future growth.**

Avoid selecting technology because it is fashionable.

Select it because it solves a clearly defined business problem.

---

**End of Part 4**

The next part will cover:

- **OLTP vs OLAP**
- **Data Warehouse**
- **Data Lake**
- **Data Lakehouse**
- **Data Mart**
- **ETL vs ELT**
- **Data Pipeline**
- **Real Enterprise Analytics Architecture**
