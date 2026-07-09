
# 37. OLTP vs OLAP

> **Enterprise systems rarely use the same database for processing transactions and performing analytics.**

Why?

Because transactional workloads and analytical workloads have completely different characteristics.

Trying to optimize one database for both often results in poor performance.

---

# Two Different Business Questions

Imagine an e-commerce company.

Operational System asks:

```
Customer places an order.

↓

Can we process it?
```

Analytics System asks:

```
How many laptops were sold

during the last five years

across Asia?
```

Completely different workloads.

---

# OLTP

## Online Transaction Processing

Focuses on:

```
Running the Business
```

Examples:

- Place Order
- Make Payment
- Withdraw Money
- Update Inventory
- Register Customer

---

# Characteristics

OLTP systems handle:

- Thousands of concurrent users
- Small transactions
- Fast response times
- Frequent updates
- ACID transactions

---

# Typical Operations

```sql
INSERT

UPDATE

DELETE

SELECT (Single Row)
```

---

# Example

Customer buys:

```
Laptop
```

Application executes:

```text
Create Order

↓

Reduce Inventory

↓

Create Payment

↓

Commit
```

Transaction completes within milliseconds.

---

# OLTP Requirements

- High concurrency
- Low latency
- Strong consistency
- High availability
- Fast writes

---

# Examples

- Banking Systems
- Payment Gateways
- Airline Booking
- Hospital Management
- ERP
- CRM

---

# OLAP

## Online Analytical Processing

Focuses on:

```
Understanding the Business
```

Instead of processing transactions,

it analyzes historical data.

---

# Questions

Examples:

- Top-selling products
- Monthly revenue
- Customer churn
- Fraud detection
- Sales trends
- Business forecasting

---

# Characteristics

OLAP systems execute:

- Large scans
- Complex joins
- Aggregations
- Historical analysis
- Reporting

---

# Example Query

```sql
SELECT

country,

SUM(total_sales)

FROM sales

GROUP BY country;
```

May scan:

```
Billions

of Rows
```

---

# OLAP Requirements

- Fast reads
- Massive aggregation
- Parallel execution
- Historical storage
- Compression

---

# Example Systems

- Business Intelligence
- Executive Dashboards
- Financial Reporting
- Marketing Analytics
- Data Science
- Machine Learning

---

# OLTP vs OLAP

| Feature | OLTP | OLAP |
|----------|------|-------|
| Purpose | Run Business | Analyze Business |
| Operations | Insert/Update/Delete | Read/Aggregate |
| Data | Current | Historical |
| Query Size | Small | Large |
| Latency | Milliseconds | Seconds/Minutes |
| Users | Thousands | Analysts |
| Schema | Highly Normalized | Often Denormalized |

---

# Why Separate Them?

Imagine:

CEO executes:

```sql
Annual Sales Report
```

on production database.

Query scans:

```
5 Billion Rows
```

Meanwhile:

Customers cannot place orders.

Business impacted.

Therefore:

Separate systems.

---

# Enterprise Architecture

```text
Customers

↓

OLTP Database

↓

CDC / ETL

↓

Data Warehouse

↓

Dashboards

↓

Business Users
```

Operational traffic remains isolated.

---

# 38. Data Warehouse

> **A Data Warehouse is a centralized repository designed for analytical queries across large volumes of historical data.**

Unlike OLTP,

Data Warehouses are optimized for:

Reading.

Not writing.

---

# Data Warehouse Characteristics

- Historical data
- Read optimized
- Denormalized schema
- Large scans
- Compression
- Parallel execution

---

# Enterprise Flow

```text
Orders

Payments

Inventory

CRM

↓

ETL

↓

Data Warehouse

↓

Reports
```

All enterprise data converges.

---

# Example

Instead of querying:

10 production databases,

analysts query:

```
One Warehouse
```

---

# Benefits

- Single source for analytics
- Historical reporting
- Faster business intelligence
- Better performance
- Reduced production impact

---

# Popular Data Warehouses

| Platform | Service |
|-----------|---------|
| AWS | Redshift |
| Azure | Synapse Analytics |
| Google Cloud | BigQuery |
| Snowflake | Snowflake |
| ClickHouse | Open-source analytics DB |

---

# Star Schema

Most common warehouse model.

```text
Customer

↓

Fact Sales

↓

Product

↓

Time

↓

Region
```

Fact table in center.

Dimensions around it.

Looks like a star.

---

# Fact Table

Stores:

Business events.

Example:

Sales.

```text
SaleID

ProductID

CustomerID

DateID

Quantity

Revenue
```

Large.

Millions or billions of rows.

---

# Dimension Tables

Provide descriptive information.

Examples:

Customer

Product

Time

Store

Region

Much smaller than fact tables.

---

# Snowflake Schema

Variation of Star Schema.

Dimensions further normalized.

Example:

```text
Product

↓

Category

↓

Department
```

Less duplication.

More joins.

---

# Star vs Snowflake

| Star | Snowflake |
|-------|------------|
| Simpler | More normalized |
| Faster queries | Smaller storage |
| Fewer joins | More joins |
| Easier BI | More complex |

---

# 39. Data Lake

> **A Data Lake stores raw data in its original format.**

Unlike warehouses,

lakes store:

Everything.

---

# Supported Data

- CSV
- JSON
- Images
- Videos
- PDFs
- Sensor Data
- Audio
- Logs
- Parquet
- Avro

Structured,

Semi-structured,

Unstructured.

---

# Example

```text
IoT

↓

JSON

↓

Images

↓

Videos

↓

Logs

↓

Data Lake
```

Everything preserved.

---

# Why?

Maybe today:

No business use.

Tomorrow:

Machine Learning requires it.

---

# Characteristics

- Low-cost storage
- Massive scalability
- Raw data
- Schema applied during analysis (schema-on-read)

---

# Popular Services

| Platform | Service |
|----------|---------|
| AWS | S3 |
| Azure | Data Lake Storage |
| Google | Cloud Storage |
| Hadoop | HDFS |

---

# Advantages

- Cheap storage
- Unlimited scale
- Flexible
- ML friendly

---

# Disadvantages

Without governance:

Data Lake becomes:

```
Data Swamp
```

Messy.

Unusable.

---

# 40. Data Lakehouse

Modern architecture.

Combines:

Warehouse

+

Lake.

---

# Why?

Warehouse:

Excellent analytics.

Limited formats.

---

Lake:

Stores everything.

Poor governance.

---

Lakehouse combines:

Both.

---

# Architecture

```text
Raw Data

↓

Data Lake

↓

Lakehouse

↓

Analytics

↓

Machine Learning
```

---

# Features

- Open file formats
- ACID support
- Versioning
- Governance
- Analytics

---

# Popular Technologies

- Delta Lake
- Apache Iceberg
- Apache Hudi

---

# 41. Data Mart

A Data Mart is:

A department-specific warehouse.

---

# Example

Enterprise Warehouse.

↓

Finance Mart

↓

Marketing Mart

↓

Sales Mart

↓

HR Mart

Each team accesses only relevant data.

---

# Benefits

- Faster queries
- Security
- Business ownership
- Simplified reporting

---

# 42. ETL vs ELT

Enterprise data rarely moves directly into analytics systems.

It passes through pipelines.

---

# ETL

Extract

↓

Transform

↓

Load

---

Example

```text
Production

↓

Extract

↓

Clean

↓

Aggregate

↓

Warehouse
```

Transformation happens first.

---

# ELT

Extract

↓

Load

↓

Transform

---

Raw data loaded immediately.

Transformation later.

Preferred in modern cloud data platforms because storage is inexpensive and compute can scale independently.

---

# Comparison

| ETL | ELT |
|-----|-----|
| Transform First | Load First |
| Traditional | Modern Cloud |
| Less Storage | More Storage |
| Faster Initial Queries | Greater Flexibility |

---

# 43. Data Pipeline

A Data Pipeline moves data between systems.

---

# Example

```text
Application

↓

Kafka

↓

Stream Processing

↓

Warehouse

↓

Dashboard
```

---

# Pipeline Types

Batch

Example:

Nightly reports.

---

Streaming

Example:

Fraud Detection.

---

# Batch Processing

```text
Every Night

↓

Read Files

↓

Process

↓

Warehouse
```

High throughput.

Higher latency.

---

# Stream Processing

```text
Event

↓

Immediate Processing

↓

Dashboard
```

Low latency.

Continuous.

---

# Popular Technologies

| Technology | Purpose |
|------------|----------|
| Kafka | Streaming |
| Apache Spark | Batch & Streaming |
| Apache Flink | Real-time Stream Processing |
| Apache Beam | Unified Pipeline API |
| AWS Glue | Managed ETL |
| Azure Data Factory | Data Integration |
| Google Dataflow | Managed Pipelines |

---

# Enterprise Analytics Architecture

```text
Applications

↓

OLTP Databases

↓

CDC / Kafka

↓

Data Lake

↓

Data Warehouse

↓

Data Marts

↓

BI Dashboards

↓

Executives

↓

Business Decisions
```

---

# Common Mistakes

### Running Analytics on OLTP

Result:

Slow customer transactions.

---

### Using Warehouse as OLTP

Writes become inefficient.

---

### Ignoring Data Quality

Analytics becomes unreliable.

---

### No Governance

Data Lake becomes:

```
Data Swamp
```

---

# Architect's Perspective

Enterprise architects should think of **operational systems** and **analytical systems** as two complementary worlds.

Operational systems answer:

> **"What is happening right now?"**

Analytical systems answer:

> **"Why did it happen, and what should we do next?"**

Attempting to optimize one system for both purposes usually leads to compromise.

Modern enterprise architectures therefore separate concerns:

- OLTP for business operations.
- Data Lakes for raw storage.
- Data Warehouses for analytics.
- Data Marts for business teams.
- Streaming pipelines for real-time insights.

This separation allows each platform to evolve independently while supporting both day-to-day operations and strategic decision-making.

---

**End of Part 5**

The next part will cover:

- **Data Partitioning**
- **Horizontal Partitioning**
- **Vertical Partitioning**
- **Database Sharding**
- **Replication**
- **Read Replicas**
- **Multi-Region Data Architecture**
- **Geo-Partitioning**
- **Data Locality**
- **Cross-Region Replication**
