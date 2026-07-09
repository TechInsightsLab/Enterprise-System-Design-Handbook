
# 15. Two-Phase Commit (2PC)

> **Two-Phase Commit (2PC) is one of the earliest and most well-known protocols for implementing distributed transactions across multiple systems.**

Its objective is simple:

> **Either every participant commits, or every participant rolls back.**

2PC attempts to extend the ACID property of **Atomicity** across multiple databases or services.

Although conceptually elegant, it introduces significant operational and scalability challenges.

---

# Why Two-Phase Commit Exists

Imagine an online banking system.

Money transfer requires updating two independent databases.

```text
Bank A Database

↓

Debit ₹10,000

-------------------------

Bank B Database

↓

Credit ₹10,000
```

Possible outcomes:

✔ Debit succeeds

✔ Credit succeeds

Correct.

---

Or

✗ Debit fails

✗ Credit fails

Also acceptable.

---

But this must **never** happen:

✔ Debit succeeds

✗ Credit fails

Money disappears.

2PC was designed to prevent this.

---

# Participants

Every 2PC transaction consists of:

```text
Coordinator

↓

Participant 1

↓

Participant 2

↓

Participant 3
```

---

## Coordinator

Responsible for:

- Starting transaction
- Collecting votes
- Deciding commit
- Deciding rollback

---

## Participants

Responsible for:

- Executing local transaction
- Preparing changes
- Waiting for coordinator
- Committing or rolling back

---

# Two Phases

As the name suggests:

```
Phase 1

Prepare

↓

Phase 2

Commit
```

---

# Phase 1 — Prepare Phase

Coordinator asks:

```
Can everyone commit?
```

---

## Flow

```text
Coordinator

↓

Prepare?

↓

Payment

↓

Inventory

↓

Shipping
```

Each participant:

- Executes local work
- Does NOT commit
- Locks affected data
- Writes recovery log
- Replies:

```
YES

or

NO
```

---

# Example

Payment Service

```text
Authorize Payment

↓

Lock Records

↓

Write Log

↓

Vote YES
```

---

Inventory Service

```text
Reserve Inventory

↓

Lock Rows

↓

Write Log

↓

Vote YES
```

---

Shipping Service

```text
Prepare Shipment

↓

Lock Data

↓

Vote YES
```

---

Coordinator now has:

```text
YES

YES

YES
```

Proceed to:

Commit Phase.

---

# What If Someone Votes NO?

Example:

Inventory:

```
Out Of Stock
```

Inventory replies:

```
NO
```

Coordinator receives:

```text
Payment

YES

Inventory

NO

Shipping

YES
```

Coordinator decides:

```
ROLLBACK
```

Everyone rolls back.

---

# Phase 2 — Commit Phase

If every participant voted:

```
YES
```

Coordinator sends:

```
COMMIT
```

---

## Flow

```text
Coordinator

↓

Commit

↓

Payment

↓

Inventory

↓

Shipping
```

Each participant:

- Commits local transaction
- Releases locks
- Acknowledges completion

Transaction finished.

---

# Rollback Flow

If any participant votes:

```
NO
```

Coordinator sends:

```
ROLLBACK
```

---

Participants execute:

```text
Undo Local Changes

↓

Release Locks

↓

Finished
```

---

# Complete Success Timeline

```text
Client

↓

Coordinator

↓

Prepare

↓

YES

YES

YES

↓

Commit

↓

Committed

↓

Success
```

---

# Failure Timeline

```text
Client

↓

Coordinator

↓

Prepare

↓

YES

NO

YES

↓

Rollback

↓

Rollback Complete
```

---

# 2PC State Machine

Coordinator:

```text
Start

↓

Prepare

↓

Waiting

↓

Commit

or

Rollback

↓

Finished
```

---

Participant:

```text
Idle

↓

Prepare

↓

Waiting

↓

Commit

or

Rollback

↓

Finished
```

---

# Internal Working

Each participant stores:

```
Transaction Log
```

Example:

```text
Transaction

Prepared

Not Yet Committed
```

If server crashes,

recovery process checks log.

---

# Advantages

## Atomicity

Either:

Everything succeeds

or

Everything rolls back.

---

## Simple Mental Model

Developers understand:

```
Commit

Rollback
```

Similar to traditional databases.

---

## Strong Consistency

All participants agree.

Business correctness preserved.

---

# Disadvantages

Unfortunately,

2PC has significant limitations.

---

# Blocking Problem

Participants remain locked while waiting.

Example:

```text
Coordinator

↓

Prepare

↓

Participant

↓

Waiting...
```

Coordinator crashes.

Participants cannot decide:

- Commit?
- Rollback?

They remain blocked.

---

# Blocking Example

Payment Service:

```
Prepared
```

Inventory:

```
Prepared
```

Shipping:

```
Prepared
```

Coordinator crashes.

Everyone waits.

Potentially:

Minutes.

Hours.

---

# Long Locks

Prepared transactions hold:

- Database rows
- Tables
- Resources

Other transactions cannot proceed.

Throughput decreases.

---

# Single Point of Coordination

Coordinator controls:

Everything.

If coordinator becomes unavailable:

Entire transaction stalls.

---

# Poor Scalability

Imagine:

```text
Order

↓

Payment

↓

Inventory

↓

Shipping

↓

Tax

↓

Coupon

↓

Invoice

↓

Fraud Detection

↓

Notification
```

Ten participants.

Each waits.

Latency grows.

---

# Slowest Participant Wins

Total transaction time equals:

```
Slowest Participant
```

If one service requires:

```
5 Seconds
```

Everyone waits.

---

# Network Sensitivity

Temporary timeout.

Coordinator cannot determine:

Did participant commit?

Or crash?

Decision becomes difficult.

---

# Coordinator Failure

Worst-case scenario.

Example:

Coordinator sends:

```
COMMIT
```

to:

Payment.

Before sending to Inventory,

Coordinator crashes.

State becomes uncertain.

---

# Recovery

Coordinator restarts.

Reads:

Transaction Log.

Determines:

Previous decision.

Continues processing.

Recovery depends heavily on durable logging.

---

# Why Prepared Logs Matter

Example:

```text
Prepare

↓

Disk Log

↓

Vote YES
```

Without logging,

participant forgets:

Prepared transaction.

Correct recovery impossible.

---

# Database Support

Traditional enterprise databases support XA transactions.

Examples:

- Oracle
- IBM DB2
- SQL Server
- PostgreSQL (prepared transactions)
- MySQL (XA)

Application servers:

- WebLogic
- WildFly
- JBoss
- WebSphere

also support 2PC.

---

# Java Support

Java provides:

```text
JTA

(Java Transaction API)
```

Common implementations:

- Narayana
- Atomikos
- Bitronix

Spring integrates with:

```
JtaTransactionManager
```

for XA transactions.

---

# XA Transactions

XA is an industry specification for coordinating distributed transactions.

Architecture:

```text
Application

↓

Transaction Manager

↓

Database A

Database B

Message Queue
```

Transaction Manager performs:

Two-Phase Commit.

---

# Performance Analysis

Suppose:

Each service requires:

```
40 ms
```

Five services:

```
5 × 40 ms

↓

200 ms
```

Additional coordination:

```
Prepare

+

Commit

+

Network

+

Logging
```

Total:

Often exceeds:

```
300–500 ms
```

Modern microservices typically target:

```
<100 ms
```

2PC struggles to meet these latency goals.

---

# Where 2PC Works Well

Suitable for:

- Enterprise ERP
- Banking core systems
- Legacy monoliths
- Internal enterprise systems
- Limited number of databases
- Low-latency networks

---

# Where 2PC Performs Poorly

Poor choice for:

- Internet-scale systems
- Global deployments
- Microservices
- Event-driven systems
- Long-running workflows
- Cloud-native architectures

---

# Common Misconceptions

### Myth

2PC guarantees high availability.

**Reality**

It prioritizes atomicity over availability.

Coordinator failures can block the system.

---

### Myth

2PC scales naturally.

**Reality**

Performance decreases as participants increase.

---

### Myth

2PC solves every distributed transaction problem.

**Reality**

It solves atomic commit,

not long-running business workflows.

---

# Decision Matrix

| Requirement | 2PC Suitability |
|-------------|-----------------|
| Single Datacenter | Excellent |
| Cross-Region | Poor |
| Microservices | Poor |
| Legacy Enterprise | Good |
| Banking Core | Good |
| Long Running Transactions | Poor |
| Event-Driven Architecture | Poor |
| Cloud Native | Poor |

---

# Cost Analysis

| Aspect | Rating |
|---------|--------|
| Infrastructure Cost | Medium |
| Engineering Complexity | Medium |
| Operational Complexity | High |
| Latency | High |
| Availability | Low During Failures |
| Consistency | Excellent |
| Scalability | Poor |

---

# Architect's Perspective

Two-Phase Commit was designed in an era when:

- Applications were monolithic.
- Databases were centralized.
- Networks were reliable.
- Services were tightly coupled.

Modern distributed systems emphasize:

- Independent deployment
- Event-driven communication
- High availability
- Horizontal scalability

As a result,

2PC is now used primarily in **legacy enterprise environments** and specialized financial systems.

For most modern cloud-native applications, architects prefer patterns such as:

- Saga
- Outbox
- Transactional Messaging
- Compensation

These patterns sacrifice global ACID transactions in favor of scalability and resilience.

---

**End of Part 3A**

The next section will cover:

- **Three-Phase Commit (3PC)**
- **Limitations of 2PC**
- **Why Microservices Avoid XA**
- **Saga Pattern**
- **Orchestration vs Choreography**
- **Compensation Transactions**
- **Modern Distributed Transaction Strategies**
