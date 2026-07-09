
# 33. Consistency Monitoring

> **Consistency is not a one-time architectural decision—it is a continuously monitored operational characteristic.**

Many organizations monitor:

- CPU
- Memory
- Disk
- Network

Very few monitor:

- Replica lag
- Stale reads
- Replication failures
- Quorum failures
- Split-brain indicators

These metrics determine whether customers can trust the data.

---

# Consistency Monitoring Lifecycle

```text
Write

↓

Replication

↓

Synchronization

↓

Validation

↓

Monitoring

↓

Alerting

↓

Recovery
```

Every stage should be observable.

---

# What Should Be Monitored?

Consistency monitoring spans multiple layers.

```text
Business

↓

Application

↓

Database

↓

Replication

↓

Infrastructure
```

Problems at any layer can cause inconsistent data.

---

# Database Metrics

Examples:

- Replication Lag
- Write Latency
- Read Latency
- Failed Replications
- Leader Changes
- Transaction Rollbacks
- Lock Wait Time
- Commit Time

---

# Replication Lag

One of the most important consistency metrics.

Definition:

```
Primary Write Time

↓

Replica Apply Time
```

Difference:

```
Replication Lag
```

---

## Example

Primary:

```
10:00:00
```

Replica:

```
10:00:05
```

Lag:

```
5 Seconds
```

Reads from the replica may be stale.

---

# Why Replica Lag Matters

Suppose:

Customer changes password.

Replica lags:

```
15 Seconds
```

Customer logs in through another region.

Authentication fails.

The system is available,

but inconsistent.

---

# Read Freshness

Monitor:

```
Newest Version

↓

Replica Version
```

Freshness determines:

How old replica data is.

---

# Leader Health

Monitor:

- Leader availability
- Election frequency
- Heartbeat latency
- Leadership changes

Frequent elections often indicate instability.

---

# Quorum Health

Questions:

- Is quorum available?
- Are majority writes succeeding?
- Are majority reads succeeding?

Loss of quorum usually indicates serious operational problems.

---

# Consensus Metrics

Examples:

- Election duration
- Commit latency
- Failed elections
- Heartbeat timeout
- Proposal failures

These are essential for:

- Raft
- Paxos
- etcd
- ZooKeeper

---

# Distributed Lock Metrics

Monitor:

- Lock acquisition time
- Lock contention
- Lock expiration
- Deadlocks

Long lock times often reduce throughput.

---

# Business Consistency Metrics

Technical consistency is insufficient.

Measure:

- Duplicate orders
- Duplicate payments
- Inventory mismatches
- Failed transfers
- Missing events
- Lost messages

These directly affect customers.

---

# Consistency Dashboard

Example

```text
Replication Lag

250 ms

↓

Leader Status

Healthy

↓

Replica Health

5 / 5

↓

Quorum

Available

↓

Election Count

0

↓

Stale Reads

0.02%

↓

Duplicate Orders

0
```

---

# 34. Observability

Observability helps answer:

> **Why is the system inconsistent?**

---

# Metrics

Examples:

- Replication Lag
- Commit Latency
- Write Failures
- Read Failures
- Replica Count
- Leader Elections

---

# Logs

Collect:

- Write operations
- Replication events
- Consensus events
- Leader elections
- Conflict resolution
- Transaction failures

---

# Distributed Tracing

Trace:

```text
Gateway

↓

Order Service

↓

Inventory

↓

Payment

↓

Database
```

Trace reveals:

Where consistency delays occur.

---

# Event Correlation

Example

Customer reports:

```
Wrong Balance
```

Trace links:

- API
- Database
- Replication
- Queue
- Notification

Entire transaction becomes visible.

---

# Alerting

Alerts should focus on customer impact.

Good alerts:

- Replica lag > 3 seconds
- Leader unavailable
- Quorum lost
- Split-brain suspected
- Duplicate payments detected
- Replication stopped

Avoid alerting solely on CPU or memory when consistency is unaffected.

---

# 35. Production Incidents

Real systems frequently experience consistency failures.

Learning from these incidents improves architecture.

---

# Incident 1 — Stale Inventory

## Situation

Inventory updates propagated asynchronously.

Customer A purchased:

```
Last Laptop
```

Replica lag:

```
8 Seconds
```

Customer B:

Purchased same laptop.

---

## Result

Overselling.

Manual refund.

Customer dissatisfaction.

---

## Root Cause

Replica lag.

---

## Improvement

Inventory reservation moved to:

Strong consistency.

---

# Incident 2 — Duplicate Payment

Customer retried payment.

Network timeout occurred.

Payment actually succeeded.

Retry created:

Second payment.

---

## Root Cause

No idempotency key.

---

## Improvement

Introduced:

- Idempotency keys
- Request deduplication
- Transaction reconciliation

---

# Incident 3 — Split-Brain

Network partition.

Two leaders accepted writes.

Both regions believed they were primary.

---

## Result

Conflicting updates.

Manual reconciliation required.

---

## Improvement

Consensus-based leader election.

---

# Incident 4 — Replica Failure

Replica unavailable.

Read traffic redirected.

Primary overloaded.

Latency increased.

---

## Lesson

Read scalability requires healthy replicas.

---

# Incident 5 — Event Ordering

Event stream:

```
Order Delivered

↓

Order Shipped
```

Customers became confused.

---

## Root Cause

Events from multiple partitions.

---

## Improvement

Partition by:

Order ID.

Ordering restored.

---

# Lessons Learned

| Incident | Improvement |
|-----------|-------------|
| Stale Reads | Strong consistency where required |
| Duplicate Payments | Idempotency |
| Split-Brain | Consensus |
| Replica Failure | Capacity Planning |
| Event Ordering | Correct Partition Strategy |

---

# 36. Common Anti-patterns

> **Most consistency failures originate from poor architectural assumptions rather than database bugs.**

---

# Anti-pattern 1

## Reading From Replicas Immediately After Writes

```text
Write

↓

Primary

↓

Read

↓

Replica
```

Replica may be stale.

---

### Better

Read from primary

or

Use Read-after-Write consistency.

---

# Anti-pattern 2

## Assuming Clocks Are Synchronized

Using timestamps alone for ordering.

Problem:

Different machines have different clocks.

---

### Better

Use:

- Logical clocks
- Vector clocks
- Consensus ordering

---

# Anti-pattern 3

## Infinite Retries

Retrying non-idempotent operations.

Example:

```
Charge Card

↓

Timeout

↓

Retry

↓

Duplicate Charge
```

---

### Better

Retries +

Idempotency Keys.

---

# Anti-pattern 4

## Multi-Leader Without Conflict Resolution

Every region accepts writes.

No merge strategy.

Eventually:

Conflicting data.

---

### Better

Define conflict resolution before deployment.

---

# Anti-pattern 5

## Ignoring Replica Lag

Application assumes:

Replica always current.

Reality:

Reads become stale.

---

### Better

Monitor lag continuously.

---

# Anti-pattern 6

## Using Strong Consistency Everywhere

Every operation waits for all replicas.

Result:

- High latency
- Reduced availability
- Poor scalability

---

### Better

Choose consistency per business workflow.

---

# Anti-pattern Summary

| Anti-pattern | Better Practice |
|---------------|----------------|
| Read Immediately from Replica | Read Primary |
| Clock-based Ordering | Logical Ordering |
| Retry Without Idempotency | Retry + Idempotency |
| Multi-Leader Without Merge | Conflict Resolution |
| Ignore Replica Lag | Continuous Monitoring |
| Strong Everywhere | Business-driven Consistency |

---

# 37. Architecture Review Checklist

Before approving a distributed design, verify:

---

## Business

- [ ] Which workflows require strong consistency?
- [ ] Which tolerate eventual consistency?
- [ ] What is the cost of stale data?
- [ ] What is the acceptable replication delay?

---

## Database

- [ ] Replication strategy defined?
- [ ] Replica lag monitored?
- [ ] Quorum configured?
- [ ] Backup consistency verified?
- [ ] Leader election tested?

---

## Application

- [ ] Idempotency implemented?
- [ ] Retry strategy defined?
- [ ] Read-after-write guaranteed where required?
- [ ] Conflict resolution documented?

---

## Messaging

- [ ] Event ordering guaranteed?
- [ ] Duplicate events handled?
- [ ] Replay supported?

---

## Operations

- [ ] Replica lag dashboards
- [ ] Quorum monitoring
- [ ] Leader election alerts
- [ ] Runbooks available

---

# 38. Production Readiness Checklist

```markdown
Replication

- [ ] Configured
- [ ] Tested
- [ ] Replica lag monitored

Consensus

- [ ] Leader election tested
- [ ] Split-brain prevented

Application

- [ ] Idempotency
- [ ] Retry policy
- [ ] Read-after-write

Monitoring

- [ ] Metrics
- [ ] Logs
- [ ] Tracing
- [ ] Dashboards

Recovery

- [ ] Runbooks
- [ ] Disaster Recovery
- [ ] Chaos Testing
```

---

# 39. Architecture Decision Record (ADR)

## ADR-007

### Title

Adopt Quorum-Based Leaderless Replication

---

### Context

The platform serves users globally.

Requirements:

- High availability
- Multi-region writes
- Eventual convergence

---

### Decision

Use:

- Leaderless replication
- Quorum reads
- Quorum writes
- Read repair
- Hinted handoff

---

### Alternatives

Leader-based replication.

Rejected because:

Regional write latency too high.

---

### Consequences

Positive:

- Better availability
- Regional independence
- Horizontal scalability

Negative:

- Conflict resolution
- Operational complexity
- Eventual consistency

---

### Success Criteria

- Replica lag < 500 ms
- Conflict rate < 0.01%
- Read latency < 100 ms

---

# 40. Consistency Decision Tree

```text
Is stale data acceptable?

↓

NO

↓

Strong Consistency

↓

Leader

↓

Consensus

↓

Quorum

------------------------

YES

↓

Can users tolerate temporary inconsistency?

↓

YES

↓

Eventual Consistency

↓

Leaderless

↓

Read Repair

↓

Conflict Resolution
```

---

# Architecture Scorecard

| Evaluation Area | Questions |
|-----------------|-----------|
| Business Alignment | Is the consistency model appropriate for business risk? |
| Data Correctness | Can stale reads cause financial or legal issues? |
| Availability | Does the system remain operational during failures? |
| Latency | Does the chosen model meet response time targets? |
| Scalability | Can the system scale globally? |
| Conflict Resolution | Are concurrent updates handled correctly? |
| Monitoring | Are replication and quorum continuously monitored? |
| Operability | Can operators diagnose and recover consistency issues quickly? |

---

# Architect's Perspective

Consistency is not a binary property.

It is a **business decision expressed through architecture**.

The most successful systems combine:

- Strong consistency where correctness is critical.
- Eventual consistency where scalability is more important.
- Read-after-write consistency for user experience.
- Consensus for coordination.
- Quorum for balanced trade-offs.
- Continuous monitoring to detect drift before customers do.

The objective is not to eliminate inconsistency completely.

The objective is to ensure that **every consistency guarantee matches the business consequences of stale or conflicting data**.

---


**End of Part 7**

The next section will cover:

- **Enterprise Case Studies (Google Spanner, Amazon DynamoDB, Cassandra, CockroachDB, MongoDB, Kafka)**
- **Architecture Evolution Timeline**
- **Cloud Mapping**
- **Technology Comparisons**
- **Cost Analysis**
- **Common Myths**
- **Interview Questions (Beginner → Principal Architect)**
- **Revision Cheat Sheet**
- **Chapter Summary**
- **Chapter Completion Checklist**
- **Transition to Chapter 8 – Distributed Transactions**
