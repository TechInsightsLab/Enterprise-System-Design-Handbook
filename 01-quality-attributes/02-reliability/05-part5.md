
# 14. Mechanisms

> **Architecture decisions explain *why* a system should behave in a particular way. Mechanisms explain *how* those architectural decisions are implemented.**

There is no single technology that makes a system reliable.

Reliability emerges from the combination of:

- Good architecture
- Correct implementation
- Operational discipline
- Continuous verification

Different organizations may use different technologies while implementing exactly the same architectural principles.

For example:

| Architectural Decision | Possible Technologies |
|------------------------|-----------------------|
| ACID Transactions | PostgreSQL, MySQL, Oracle, SQL Server |
| Event Streaming | Apache Kafka, Amazon MSK, Azure Event Hubs |
| Messaging | RabbitMQ, Amazon SQS, Azure Service Bus |
| Idempotency Store | Redis, PostgreSQL, DynamoDB |
| Audit Logs | Event Store, Kafka, Database Tables |
| Distributed Locking | Redis, ZooKeeper, etcd |
| Monitoring | Prometheus, Datadog, CloudWatch, Azure Monitor |

Technology evolves.

Architectural principles remain relatively constant.

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

Continuous Verification
```

Technology should always be the final decision.

---

# 14.1 Relational Databases

Relational databases remain one of the most reliable mechanisms for protecting business data.

They implement decades of research in:

- Transactions
- Consistency
- Recovery
- Durability
- Concurrency

---

## Why Architects Choose Relational Databases

Business operations such as:

- Payments
- Banking
- Orders
- Invoices
- Inventory

require strong consistency.

Relational databases provide:

- ACID transactions
- Constraints
- Foreign keys
- Rollback
- Recovery logs

---

## Reliability Mechanisms

Examples include:

- Write-Ahead Logging (WAL)
- Transaction Logs
- Crash Recovery
- Locks
- MVCC (Multi-Version Concurrency Control)
- Replication

---

## Example

```text
Begin Transaction

↓

Update Account A

↓

Update Account B

↓

Commit
```

If failure occurs before commit:

Entire transaction rolls back.

Business correctness is preserved.

---

# 14.2 Redis

Redis is commonly viewed as a cache.

However, it also provides several mechanisms that improve Reliability.

---

## Reliable Use Cases

- Idempotency Keys
- Distributed Locks
- Session State
- Rate Limiting
- Temporary Business State

---

## Example

```
Idempotency Key

↓

Redis

↓

Already Exists?

↓

Yes

↓

Return Previous Response
```

Duplicate processing is prevented.

---

## Important Principle

Redis should improve Reliability.

It should not become the sole source of business truth.

Critical business records belong in durable storage.

---

# 14.3 Apache Kafka

Kafka is designed for reliable event streaming.

Instead of direct service communication:

```text
Order Service

↓

Kafka

↓

Inventory

↓

Billing

↓

Notification
```

---

## Reliability Mechanisms

Kafka provides:

- Persistent Logs
- Replication
- Offset Tracking
- Replay Capability
- Ordering Within Partition

---

## Why Architects Use Kafka

Benefits include:

- No message loss after acknowledgment
- Event replay
- Audit capability
- Decoupling
- Event history

---

## Example

```
Order Created

↓

Kafka Topic

↓

Consumer Failure

↓

Restart Consumer

↓

Replay Event
```

Business events are not lost.

---

# 14.4 RabbitMQ

RabbitMQ focuses on reliable message delivery.

Unlike Kafka's event log model,

RabbitMQ emphasizes:

- Guaranteed delivery
- Flexible routing
- Acknowledgements
- Dead Letter Queues

---

## Reliability Features

- Persistent Messages
- Durable Queues
- Consumer Acknowledgements
- Publisher Confirms
- Quorum Queues

---

## Message Lifecycle

```text
Producer

↓

RabbitMQ

↓

Consumer

↓

Acknowledgement

↓

Remove Message
```

Until acknowledged,

The broker assumes processing has not completed.

---

# 14.5 Spring Boot

Spring Boot provides several mechanisms supporting Reliability.

---

## Transaction Management

```java
@Transactional
```

Ensures:

- Commit
- Rollback
- Atomicity

---

## Validation

Spring Validation protects business rules before processing.

Examples:

- Required fields
- Range validation
- Format validation
- Custom business validation

---

## Retry Support

Spring Retry enables controlled retries.

Recommended configuration:

- Retry Limit
- Exponential Backoff
- Jitter
- Exception Filtering

---

## Resilience4j

Provides mechanisms including:

- Retry
- Circuit Breaker
- Bulkhead
- Time Limiter
- Rate Limiter

These prevent dependency failures from compromising correctness.

---

# 14.6 Kubernetes

Kubernetes primarily improves Availability.

However,

Several mechanisms also contribute to Reliability.

---

## StatefulSets

Stateful workloads require:

- Stable identities
- Ordered deployment
- Persistent volumes

Suitable for:

- Databases
- Kafka
- ZooKeeper

---

## Persistent Volumes

Business data should survive:

- Pod restart
- Node replacement
- Rolling deployment

Persistent Volumes provide durable storage.

---

## Pod Disruption Budgets

Prevent excessive simultaneous disruption.

Business services maintain minimum healthy replicas.

---

## Graceful Shutdown

Applications complete:

- Active transactions
- Message acknowledgements
- Database commits

before termination.

---

# 14.7 AWS

AWS provides numerous managed reliability mechanisms.

| Requirement | AWS Service |
|-------------|-------------|
| Durable Storage | Amazon S3 |
| Relational Database | Amazon RDS |
| Distributed Database | DynamoDB |
| Event Streaming | Amazon MSK |
| Messaging | Amazon SQS |
| Notifications | Amazon SNS |
| Workflow | AWS Step Functions |
| Monitoring | CloudWatch |

---

## Example Reliable Architecture

```text
Client

↓

Application Load Balancer

↓

Application

↓

Amazon RDS

↓

Outbox Table

↓

Amazon MSK

↓

Consumers
```

---

# 14.8 Azure

Azure provides similar capabilities.

Examples include:

| Requirement | Azure Service |
|-------------|---------------|
| Database | Azure SQL |
| Distributed DB | Cosmos DB |
| Messaging | Service Bus |
| Event Streaming | Event Hubs |
| Storage | Blob Storage |
| Workflow | Logic Apps |
| Monitoring | Azure Monitor |

---

# 14.9 Google Cloud Platform

Common services include:

| Requirement | GCP Service |
|-------------|-------------|
| Relational Database | Cloud SQL |
| Distributed Database | Spanner |
| Messaging | Pub/Sub |
| Storage | Cloud Storage |
| Monitoring | Cloud Monitoring |
| Kubernetes | GKE |

---

# 14.10 Observability

Reliability depends on visibility.

Without observability,

Incorrect behavior may remain undetected.

---

## Metrics

Examples include:

- Failed Transactions
- Duplicate Requests
- Error Rate
- Retry Count
- Queue Depth
- Data Validation Failures

---

## Logs

Reliable logs should include:

- Correlation ID
- Business ID
- User ID
- Transaction ID
- Event ID

---

## Distributed Tracing

Tracing helps answer:

> Which service introduced incorrect behavior?

Rather than simply identifying where latency occurred.

---

# 14.11 Schema Validation

Reliable systems validate messages before processing.

Example:

```
Incoming Event

↓

Schema Validation

↓

Accept

or

Reject
```

This prevents malformed events from corrupting downstream systems.

---

# 14.12 Checksums and Hashing

Checksums verify integrity.

Examples:

- File Upload
- Database Replication
- Backup Validation

Example:

```
Original SHA-256

↓

Transferred File

↓

Calculated SHA-256

↓

Match?
```

---

# 14.13 Feature Flags

Feature Flags improve Reliability during deployments.

Instead of rollback:

Disable problematic functionality.

Example:

```
New Recommendation Engine

↓

Incorrect Results

↓

Feature Flag OFF
```

Core business continues operating correctly.

---

# 14.14 Automated Reconciliation

Many enterprise systems perform scheduled reconciliation.

Examples:

Banking

```
Payments

=

Ledger
```

Inventory

```
Inventory Events

=

Current Stock
```

Differences generate alerts.

---

# Mechanism Selection Guidelines

| Requirement | Recommended Mechanism |
|-------------|----------------------|
| Prevent Duplicate Processing | Idempotency |
| Reliable Messaging | Kafka / RabbitMQ |
| Strong Transactions | Relational Database |
| Complete Audit History | Event Sourcing |
| Durable Storage | RDBMS / Object Storage |
| Safe Retry | Idempotency + Retry |
| Concurrent Updates | Optimistic Locking |
| Integrity Validation | Checksums |
| Workflow Recovery | Saga |
| Failed Message Handling | Dead Letter Queue |

---

# 15. Decision Matrix

Different organizations require different Reliability strategies.

The correct architecture depends on:

- Business criticality
- Regulatory obligations
- Engineering maturity
- Budget
- Scale
- Operational capabilities

---

## Reliability Decision Matrix

| Scenario | Recommendation | Why | When Not |
|----------|----------------|-----|----------|
| Startup MVP | Relational Database + ACID Transactions + Daily Backups | Simplicity and correctness with low operational overhead | Global-scale event-driven platforms |
| SME | ACID Transactions + Idempotent APIs + Reliable Messaging | Balance between reliability and development speed | Systems requiring extreme throughput and global distribution |
| Enterprise | Event-Driven Architecture + Outbox Pattern + Saga + Audit Trails | Supports distributed workflows with strong business traceability | Small, tightly coupled applications |
| Banking | Strong Consistency, ACID Transactions, Immutable Audit Logs, Multi-Level Validation | Financial correctness is mandatory | Applications where eventual consistency is acceptable |
| Healthcare | Transactional Databases, Audit Logs, Versioning, Strong Validation | Protect patient safety and regulatory compliance | Low-risk internal tools |
| Government | Immutable Records, Digital Signatures, Multi-Level Auditing, Long-Term Data Retention | Legal accountability and traceability | Temporary or disposable workloads |
| Global SaaS | Idempotent APIs, Event Streaming, Multi-Region Replication, Automated Reconciliation | Supports global scale while maintaining business correctness | Small regional applications with limited traffic |

---

# Reliability Decision Tree

```text
              Business Critical?

                /          \

             Yes            No

              │              │

       Financial Impact?   Basic Validation

         /        \

       Yes         No

       │            │

Strong Consistency  Eventual Consistency

       │

Distributed Workflow?

      /          \

    Yes          No

     │            │

Saga + Outbox   ACID Transaction

     │

Need Audit History?

     /          \

   Yes           No

    │             │

Event Sourcing   Transaction Log
```

---

# Key Takeaways

- Mechanisms are implementations of architectural decisions, not replacements for them.
- A reliable system combines transactional integrity, idempotent processing, durable messaging, observability, and continuous verification.
- Managed cloud services simplify implementation but do not eliminate the need for sound architectural reasoning.
- The appropriate mechanism depends on business requirements, regulatory constraints, and operational maturity—not on technology popularity.

---

**End of Part 5**

The next part will cover:

- **16. Trade-offs**
- **17. Measurements (MTBF, MTTF, Reliability calculations, data integrity metrics, transaction success rates, duplicate processing rate, etc.)**
- **18. Operational Considerations** (monitoring, reconciliation, testing, observability, disaster recovery, chaos engineering, and continuous verification).
