
# 19. Production Incidents

> **Every reliability incident reveals assumptions that the architecture made about the real world.**

Unlike availability incidents, reliability incidents are often difficult to detect.

The application may remain online.

CPU utilization may remain low.

Health checks may report healthy.

Yet the system may silently produce incorrect business outcomes.

These failures frequently remain undiscovered until:

- Customers complain.
- Financial reconciliation fails.
- Compliance audits identify discrepancies.
- Data validation detects inconsistencies.
- Business reports become inaccurate.

A mature engineering organization conducts **blameless post-incident reviews**, focusing on improving systems rather than assigning blame.

---

# Reliability Incident Lifecycle

```text
Business Operation

↓

Unexpected Failure

↓

Incorrect Business State

↓

Detection

↓

Impact Assessment

↓

Containment

↓

Recovery

↓

Root Cause Analysis

↓

Architecture Improvements

↓

Verification
```

Reliable organizations optimize every stage of this lifecycle.

---

# Case Study 1 — Duplicate Payment Processing

## Business

An online payment platform processes approximately **3 million transactions daily**.

Customers expect every payment to be processed exactly once.

---

## Timeline

| Time | Event |
|------|-------|
| 09:42 | Payment request sent |
| 09:42 | Payment gateway authorizes transaction |
| 09:42 | Response delayed because of network latency |
| 09:43 | Mobile application retries automatically |
| 09:43 | Payment processed a second time |
| 10:15 | Customer reports duplicate charge |

---

## Root Cause

The API lacked idempotency.

Retries created duplicate business operations.

---

## Architecture Flaw

```text
Client

↓

Payment API

↓

Network Timeout

↓

Retry

↓

Payment Executed Again
```

---

## Resolution

Implemented:

- Idempotency Keys
- Duplicate transaction detection
- Retry policy improvements
- Request tracing
- Payment reconciliation

---

## Lessons Learned

Retries are inevitable.

Duplicate business operations should never be.

---

# Case Study 2 — Lost Inventory Update

## Business

An e-commerce platform publishes inventory events after successful orders.

---

## Timeline

| Time | Event |
|------|-------|
| 11:20 | Order committed to database |
| 11:20 | Event publication attempted |
| 11:20 | Kafka broker temporarily unavailable |
| 11:20 | Event publication failed |
| 13:00 | Inventory mismatch detected |

---

## Root Cause

Database update succeeded.

Event publication failed.

The architecture assumed both operations would always succeed.

---

## Architecture Flaw

```text
Save Order

↓

Publish Event

↓

Failure

↓

Inventory Never Updated
```

---

## Resolution

Implemented:

- Transactional Outbox Pattern
- Background publisher
- Replay mechanism
- Monitoring for unpublished events

---

## Lessons Learned

Business events should never depend on immediate network availability.

---

# Case Study 3 — Race Condition in Inventory

## Business

A flash sale offered a limited quantity of a popular product.

---

## Timeline

| Time | Event |
|------|-------|
| 12:00 | Sale begins |
| 12:00 | Thousands of concurrent requests arrive |
| 12:01 | Multiple requests read identical inventory values |
| 12:02 | Inventory becomes negative |
| 12:10 | Customers receive cancellation emails |

---

## Root Cause

Concurrent updates occurred without synchronization.

---

## Resolution

Implemented:

- Optimistic locking
- Atomic inventory updates
- Reservation model
- Retry on version conflict

---

## Lessons Learned

Concurrency bugs rarely appear during development.

They emerge under production traffic.

---

# Case Study 4 — Silent Data Corruption

## Business

A financial reporting system aggregated daily transactions.

---

## Timeline

| Time | Event |
|------|-------|
| Day 1 | Deployment introduces calculation bug |
| Day 5 | Finance notices reporting discrepancies |
| Day 6 | Audit identifies incorrect totals |
| Day 7 | Root cause isolated |

---

## Root Cause

A software defect produced incorrect aggregation logic.

The system remained fully available.

---

## Resolution

Implemented:

- Automated reconciliation
- Financial validation rules
- Canary validation
- Independent reporting verification

---

## Lessons Learned

Availability dashboards cannot detect incorrect business calculations.

Business validation is equally important.

---

# Common Reliability Incident Patterns

| Observation | Lesson |
|-------------|---------|
| Duplicate requests | Implement idempotency |
| Lost events | Use Outbox Pattern |
| Concurrent updates | Protect shared state |
| Incorrect calculations | Validate business rules |
| Missing audit records | Record immutable events |
| Silent inconsistencies | Perform continuous reconciliation |

---

# 20. Anti-patterns

> **Reliable architectures are often defined more by what they avoid than by the technologies they adopt.**

Anti-patterns frequently work well during early development but become significant reliability risks in production.

---

# 20.1 Blind Retries

## Why Teams Choose It

Retries appear to improve resilience.

Developers assume repeated attempts will eventually succeed.

---

## Why It Fails

Without idempotency:

- Duplicate payments
- Duplicate orders
- Duplicate notifications

---

## Better Alternative

- Idempotent APIs
- Retry budgets
- Exponential backoff
- Request identifiers

---

# 20.2 Trusting External Systems Completely

Some applications assume third-party systems always behave correctly.

Examples:

- Payment gateways
- Shipping providers
- Tax calculation APIs

---

## Problems

- Incorrect responses
- Duplicate callbacks
- Temporary inconsistencies
- Contract violations

---

## Better Alternative

Validate every externally received result before accepting it as business truth.

---

# 20.3 Missing Audit Trail

Without historical records:

- Incidents cannot be investigated.
- Compliance becomes difficult.
- Data reconstruction becomes impossible.

---

## Better Alternative

Maintain immutable audit records for every important business operation.

---

# 20.4 Business Logic in Multiple Services

When identical business rules exist in multiple services:

Different implementations eventually diverge.

Examples:

```
Discount Calculation

↓

Order Service

↓

Invoice Service

↓

Reporting Service
```

Three slightly different implementations eventually produce inconsistent results.

---

## Better Alternative

Centralize business rules or share validated domain libraries where appropriate.

---

# 20.5 Ignoring Version Conflicts

Concurrent updates overwrite each other.

Result:

Lost customer changes.

---

## Better Alternative

Use:

- Version numbers
- Optimistic locking
- Conflict detection

---

# 20.6 Fire-and-Forget Messaging

Applications publish events without verifying successful delivery.

Problems:

- Lost events
- Missing downstream processing
- Silent business failures

---

## Better Alternative

Use:

- Publisher confirmations
- Transactional Outbox
- Monitoring
- Replay capability

---

# 20.7 Treating Cache as Source of Truth

Caches improve performance.

They should not define business correctness.

Problems include:

- Stale balances
- Incorrect inventory
- Expired business state

---

## Better Alternative

Treat durable storage as the source of truth.

Caches should accelerate reads rather than define correctness.

---

# 20.8 Skipping Validation

Developers occasionally trust client applications.

Examples:

- Negative payment amounts
- Invalid dates
- Impossible inventory quantities

---

## Better Alternative

Validate data:

- At the API boundary
- Inside the domain
- Before persistence

Never rely solely on client-side validation.

---

# 20.9 No Reconciliation

Some organizations assume:

"If the application didn't report an error, everything is correct."

Reality differs.

Silent inconsistencies occur.

---

## Better Alternative

Regularly compare:

- Payments vs Ledger
- Orders vs Inventory
- Shipments vs Tracking
- Reports vs Source Data

---

# Anti-pattern Summary

| Anti-pattern | Consequence | Better Alternative |
|--------------|------------|--------------------|
| Blind Retries | Duplicate processing | Idempotency |
| Trust External Systems | Incorrect business state | Validation |
| Missing Audit Trail | Impossible investigations | Immutable audit logs |
| Duplicated Business Rules | Inconsistent behavior | Shared domain logic |
| Ignoring Version Conflicts | Lost updates | Optimistic locking |
| Fire-and-Forget Messaging | Lost events | Outbox Pattern |
| Cache as Source of Truth | Stale data | Durable persistence |
| Missing Validation | Invalid business state | Multi-layer validation |
| No Reconciliation | Silent failures | Continuous verification |

---

# 21. Resource Impact Analysis

Reliability mechanisms improve correctness.

They also consume resources.

Architects must understand these costs.

---

# CPU Impact

Additional CPU is consumed by:

- Validation
- Cryptographic hashing
- Checksums
- Serialization
- Event processing
- Audit logging
- Reconciliation jobs

---

# Memory Impact

Reliability mechanisms require memory for:

- Idempotency stores
- Retry queues
- Message buffers
- In-memory locks
- Event caches

---

# Storage Impact

Reliable systems intentionally store more information.

Examples include:

- Audit logs
- Event history
- Transaction logs
- Dead Letter Queues
- Backups
- Snapshots
- Version history

Storage requirements increase significantly over time.

---

# Network Impact

Reliable distributed systems generate additional traffic through:

- Replication
- Event publication
- Message acknowledgements
- Heartbeats
- Consensus protocols
- Cross-region synchronization
- Reconciliation

---

# Cloud Cost Impact

| Mechanism | Cost Impact |
|-----------|------------:|
| Transaction Logs | Medium |
| Event Streaming | Medium |
| Audit Storage | Medium to High |
| Multi-Region Replication | High |
| Dead Letter Queues | Low |
| Continuous Reconciliation | Medium |
| Monitoring & Tracing | Medium |

---

# Operational Cost

Reliable systems require ongoing operational investment.

Examples include:

- Compliance reviews
- Audit verification
- Data reconciliation
- Production monitoring
- Backup validation
- Recovery exercises
- Incident investigations

These activities increase operational cost but significantly reduce long-term business risk.

---

# Reliability Cost Curve

```text
Business Reliability ↑

Near Perfect
                      ●

                   ●

                ●

             ●

          ●

Basic Reliability ●

────────────────────────────────────────→

Engineering Cost + Operational Complexity
```

Beyond a certain point, achieving additional reliability requires disproportionately greater investment.

---

# 22. Enterprise Maturity Model

Reliability capabilities evolve as organizations grow.

They are rarely implemented all at once.

```text
Startup

↓

Growing Company

↓

Enterprise

↓

Global Platform
```

---

## Level 1 — Startup

### Characteristics

- Small engineering team
- Rapid delivery
- Limited compliance requirements
- Few production systems

### Reliability Focus

- ACID database
- Automated backups
- Basic validation
- Error logging

Primary objective:

Protect critical business data with minimal operational complexity.

---

## Level 2 — Growing Company

### Characteristics

- Multiple products
- Increasing traffic
- Larger engineering teams

### Reliability Improvements

- Idempotent APIs
- Message queues
- Audit logs
- Retry policies
- Centralized monitoring

Business correctness becomes increasingly important as transaction volume grows.

---

## Level 3 — Enterprise

### Characteristics

- Multiple business domains
- Regulatory oversight
- Distributed systems

### Reliability Capabilities

- Event-driven architecture
- Outbox Pattern
- Saga
- Automated reconciliation
- Distributed tracing
- Continuous validation

Correctness becomes an organizational capability rather than an application feature.

---

## Level 4 — Global Platform

### Characteristics

- Worldwide operations
- Millions of daily transactions
- Strict regulatory requirements

### Reliability Practices

- Immutable event logs
- Continuous reconciliation
- Automated recovery
- Multi-region validation
- Formal data governance
- Dedicated Reliability Engineering teams

Reliability becomes a strategic business differentiator.

---

# Maturity Comparison

| Capability | Startup | Growing | Enterprise | Global |
|------------|---------|----------|------------|--------|
| Validation | Basic | Good | Advanced | Policy Driven |
| Audit | Limited | Standard | Enterprise | Immutable |
| Messaging | Minimal | Reliable | Event-Driven | Globally Replicated |
| Reconciliation | Manual | Scheduled | Automated | Continuous |
| Recovery | Manual | Assisted | Automated | Autonomous |
| Observability | Logs | Metrics | Full Tracing | Predictive Analytics |

---

# 23. Architecture Evolution

Reliable architectures evolve with business complexity.

```text
Single Database

↓

Transactional System

↓

Audit Logging

↓

Reliable Messaging

↓

Event-Driven Architecture

↓

Saga Pattern

↓

Event Sourcing

↓

Distributed Validation

↓

Continuous Reconciliation

↓

Autonomous Reliability
```

Each stage addresses new business challenges.

Organizations should adopt these capabilities as business requirements justify the additional complexity.

The objective is not architectural sophistication.

The objective is sustained business correctness at the required scale.

---

**End of Part 7**

The next part will cover:

- **24. Architecture Review Checklist**
- **25. Production Readiness Checklist**
- **26. Architecture Decision Record (ADR)**
- **27. Architecture Thinking Tips**
- **28. Architect's Mental Model**
