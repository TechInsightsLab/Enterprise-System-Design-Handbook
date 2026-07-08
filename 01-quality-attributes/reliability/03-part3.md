# 12. Failure Modes

> **Reliable systems are not designed by assuming everything works. They are designed by understanding how things fail.**

Every production system eventually experiences failures.

The objective of Reliability Engineering is **not to eliminate failures**, because that is impossible.

The objective is to ensure failures **do not produce incorrect business outcomes**.

Unlike High Availability, where failures often cause downtime, Reliability failures are frequently more dangerous because **the system continues operating while silently producing incorrect results.**

These failures are often discovered hours or days later during reconciliation, customer complaints, audits, or financial reporting.

Experienced architects therefore spend significant time identifying every possible way a system can violate business correctness.

---

# Reliability Failure Lifecycle

```text
Business Operation

↓

Fault Introduced

↓

Error Created

↓

Incorrect State

↓

Business Rule Violated

↓

Customer Impact

↓

Detection

↓

Recovery

↓

Root Cause Analysis

↓

Architecture Improvement
```

Unlike availability failures, reliability failures may remain hidden for a long time.

---

# Categories of Reliability Failures

Reliability failures generally fall into six categories.

| Category | Examples |
|-----------|----------|
| Data Integrity | Corrupted records, inconsistent balances |
| Processing Errors | Duplicate processing, missed processing |
| Communication Failures | Lost messages, duplicated events |
| Concurrency Problems | Race conditions, deadlocks |
| Configuration Errors | Incorrect feature flags, wrong routing |
| Dependency Failures | Partial writes, inconsistent state |

Understanding these categories helps architects design appropriate mitigation strategies.

---

# 12.1 Duplicate Processing

## Business Scenario

A customer clicks **Pay Now**.

The payment request reaches the payment gateway.

The gateway successfully charges the customer's credit card.

However, the response is delayed due to temporary network latency.

The mobile application assumes the request failed and automatically retries.

The payment gateway processes the request again.

The customer is charged twice.

---

## Why It Happens

Common causes include:

- Network timeout
- Client retries
- Message redelivery
- Consumer restart
- API gateway retries
- User clicking multiple times
- Browser refresh

---

## Symptoms

- Duplicate payments
- Duplicate orders
- Duplicate emails
- Duplicate shipments
- Multiple invoices

---

## Detection

Monitor:

- Duplicate transaction IDs
- Duplicate business events
- Unexpected payment volume
- Reconciliation mismatches

---

## Prevention

Architectural approaches:

- Idempotency Keys
- Unique Business Identifiers
- Request Deduplication
- Transaction Logs

---

## Recovery

Recovery often involves:

- Automatic reconciliation
- Refund processing
- Manual review
- Duplicate transaction detection

---

## Business Impact

Duplicate processing affects:

- Customer trust
- Financial accuracy
- Regulatory reporting
- Customer support workload

---

# 12.2 Lost Processing

## Business Scenario

A customer submits an order.

The application acknowledges the request.

Before persisting the order, the application crashes.

The customer believes the order succeeded.

The business never receives it.

---

## Why It Happens

Typical causes include:

- Server crash
- Power failure
- Message loss
- Database failure
- Application termination

---

## Symptoms

- Missing orders
- Missing payments
- Missing notifications
- Customer complaints

---

## Detection

Examples:

- Missing sequence numbers
- Reconciliation reports
- Audit logs
- Event replay

---

## Prevention

- Durable messaging
- Transactional persistence
- Acknowledgement after commit
- Retry mechanisms

---

## Business Impact

Silent data loss is often more damaging than temporary downtime.

---

# 12.3 Data Corruption

One of the most severe reliability failures.

Data exists.

But it is incorrect.

---

## Examples

- Negative inventory
- Incorrect account balance
- Corrupted medical records
- Invalid invoices
- Incorrect product pricing

---

## Common Causes

- Software bugs
- Concurrent writes
- Memory corruption
- Serialization errors
- Partial updates

---

## Detection

- Integrity validation
- Checksums
- Reconciliation
- Business rule validation
- Audit reports

---

## Prevention

- Database constraints
- ACID transactions
- Validation rules
- Immutable event logs
- Versioning

---

## Business Impact

Data corruption often requires:

- Manual correction
- Customer communication
- Financial adjustments
- Compliance reporting

---

# 12.4 Race Conditions

## Business Scenario

Two customers simultaneously purchase the final available product.

Inventory:

```
Available Quantity = 1
```

Both requests execute concurrently.

Each reads:

```
Quantity = 1
```

Both complete successfully.

Inventory becomes:

```
-1
```

---

## Why It Happens

Concurrent access to shared data without synchronization.

---

## Symptoms

- Overselling
- Duplicate reservations
- Incorrect balances
- Lost updates

---

## Prevention

- Optimistic Locking
- Pessimistic Locking
- Atomic Operations
- Compare-and-Swap (CAS)
- Serializable Transactions

---

## Business Impact

Incorrect business decisions become permanent records.

---

# 12.5 Lost Update

## Example

Two employees edit the same customer profile.

Employee A updates address.

Employee B updates phone number.

The second save overwrites the first.

Address changes disappear.

---

## Prevention

- Version numbers
- Optimistic locking
- Merge operations
- Conflict detection

---

# 12.6 Partial Write

Distributed systems frequently involve multiple components.

Example:

```
Payment Recorded

↓

Inventory Updated

↓

Shipping Failed
```

The business process is incomplete.

---

## Why It Happens

One operation succeeds.

Subsequent operations fail.

---

## Prevention

Architectural options:

- Saga Pattern
- Compensation
- Event-driven workflows
- Idempotent consumers

---

## Business Impact

Customers receive inconsistent experiences.

---

# 12.7 Message Loss

## Business Scenario

Order Service publishes:

```
Order Created
```

The message disappears before reaching Inventory Service.

Inventory is never reserved.

---

## Causes

- Broker failure
- Network interruption
- Consumer crash
- Incorrect acknowledgement
- Queue overflow

---

## Prevention

- Persistent messaging
- Replication
- Consumer acknowledgements
- Dead Letter Queues
- Message replay

---

## Business Impact

Invisible business failures.

---

# 12.8 Duplicate Messages

Messaging systems generally provide **At-Least-Once Delivery**.

This guarantees messages arrive.

But sometimes more than once.

---

## Prevention

Consumers should be:

- Idempotent
- Stateless when possible
- Able to detect duplicate event IDs

---

# 12.9 Stale Reads

Applications may read outdated data.

Example:

```
Primary Database

↓

Replica

(Replication Lag)

↓

Application Reads Old Value
```

---

## Business Impact

Examples:

- Incorrect inventory
- Wrong account balance
- Outdated product price

---

## Prevention

- Read-after-write consistency
- Version checks
- Primary reads for critical operations

---

# 12.10 Clock Skew

Distributed systems rarely share identical clocks.

Clock differences affect:

- Authentication
- Event ordering
- Transaction sequencing
- Audit logs

---

## Prevention

- NTP
- Hybrid Logical Clocks
- Lamport Timestamps
- Vector Clocks

---

# 12.11 Network Partition

Two healthy systems become unable to communicate.

Example:

```
Service A

X

Service B
```

Both continue operating independently.

Later synchronization becomes difficult.

---

## Problems

- Split Brain
- Conflicting writes
- Duplicate leaders
- Data inconsistency

---

## Prevention

- Consensus protocols
- Quorum
- Leader election
- Partition-aware algorithms

---

# 12.12 Split Brain

Multiple nodes incorrectly believe:

> "I am the leader."

Both accept writes.

Later synchronization becomes impossible.

---

## Prevention

- Raft
- Paxos
- ZooKeeper
- etcd
- Quorum-based writes

---

# 12.13 Configuration Errors

Many production incidents result from incorrect configuration rather than software defects.

Examples:

- Wrong database
- Incorrect API endpoint
- Disabled authentication
- Invalid feature flag

---

## Prevention

- Infrastructure as Code
- Configuration validation
- Peer review
- Progressive rollout

---

# 12.14 Dependency Failure

Reliable systems depend on external services.

Examples:

- Payment gateways
- Email providers
- SMS providers
- Identity providers

If dependencies become unreliable:

Your application becomes unreliable.

---

## Prevention

- Timeouts
- Circuit breakers
- Retries
- Fallback responses
- Graceful degradation

---

# 12.15 Human Error

Examples:

- Manual data deletion
- Wrong SQL execution
- Incorrect deployment
- Configuration mistakes

---

## Prevention

- Automation
- Approval workflows
- Least privilege
- Immutable infrastructure
- Backup validation

---

# 12.16 Software Defects

Examples include:

- Arithmetic overflow
- Null pointer exceptions
- Incorrect calculations
- Serialization bugs
- Memory corruption

---

## Prevention

- Code reviews
- Automated testing
- Static analysis
- Property-based testing
- Production monitoring

---

# 12.17 Cascading Reliability Failure

Reliability failures often propagate.

```text
Duplicate Message

↓

Duplicate Payment

↓

Incorrect Invoice

↓

Accounting Error

↓

Customer Complaint

↓

Refund

↓

Manual Investigation

↓

Audit
```

A small technical defect becomes a business problem.

---

# Reliability Failure Summary

| Failure Mode | Business Consequence | Primary Mitigation |
|--------------|----------------------|--------------------|
| Duplicate Processing | Double billing | Idempotency |
| Lost Processing | Missing business events | Durable persistence |
| Data Corruption | Incorrect records | Transactions + Validation |
| Race Condition | Inconsistent state | Locking / Atomicity |
| Lost Update | Missing changes | Versioning |
| Partial Write | Broken workflows | Saga Pattern |
| Message Loss | Missing business operations | Durable Messaging |
| Duplicate Messages | Repeated processing | Idempotent Consumers |
| Stale Reads | Incorrect decisions | Consistency controls |
| Clock Skew | Incorrect ordering | Time synchronization |
| Network Partition | Conflicting data | Consensus protocols |
| Split Brain | Data divergence | Leader election |
| Configuration Error | Incorrect behavior | Configuration management |
| Dependency Failure | Business interruption | Isolation patterns |
| Human Error | Data loss | Automation & governance |
| Software Defect | Incorrect outcomes | Testing & verification |

---

## Key Architectural Lesson

Reliable systems are not built by assuming software is bug-free.

They are built by assuming:

- Networks fail.
- Messages are duplicated.
- Requests are retried.
- Servers crash.
- Humans make mistakes.
- Dependencies become unavailable.
- Time is not perfectly synchronized.

The architect's responsibility is to ensure that **despite these realities, the business continues to receive correct, consistent, and trustworthy outcomes**.

---

**End of Part 3**

The next part will cover **Section 13: Architecture Decisions**, explaining *why* architects choose patterns such as ACID transactions, idempotency, event sourcing, CQRS, Saga, Outbox Pattern, Inbox Pattern, immutable logs, consensus protocols, validation, and audit trails to achieve Reliability before discussing implementation technologies.
