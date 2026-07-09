
# 9. Failover, Failback, Split Brain & Disaster Recovery

Replication is.

Not created.

Simply.

To make copies.

Its real purpose.

Is.

To keep.

The business running.

When failures occur.

This section.

Explains.

How replicated systems.

Survive.

Production failures.

---

# Why This Matters

Imagine.

An e-commerce platform.

Running.

On a Primary database.

```text
Application

↓

Primary Database
```

Suddenly.

The Primary crashes.

```text
💥 Primary Failure
```

Question.

Should.

The entire website.

Stop accepting orders?

Obviously not.

Replication exists.

To prevent.

Exactly this.

---

# Types of Failures

Real production systems.

Experience many failures.

```text
Failures

│

├── Server Crash

├── Disk Failure

├── VM Failure

├── Network Failure

├── Data Center Failure

├── Region Failure

├── Software Bug

└── Human Error
```

Replication.

Helps recover.

From most.

Of these.

---

# Failure Recovery Lifecycle

A replicated system.

Usually follows.

This sequence.

```text
Failure

↓

Detection

↓

Failover

↓

Recovery

↓

Failback
```

Every HA architecture.

Implements.

Some variation.

Of this flow.

---

# 9.1 Failover

> **Failover is the automatic or manual process of switching traffic from a failed primary node to a healthy replica.**

---

# Normal Operation

```text
Client

↓

Primary

↓

Replica A

↓

Replica B
```

---

# Primary Failure

```text
Primary

💥 Failed

↓

Replica A

↓

Replica B
```

Someone.

Must become.

The new Primary.

---

# Automatic Failover

Modern systems.

Usually perform.

Automatic failover.

```text
Primary

↓

Heartbeat Missing

↓

Failure Detected

↓

Leader Election

↓

Replica Promoted

↓

Traffic Redirected
```

---

# Timeline

```text
10:00:00

Primary Healthy

↓

10:00:05

Primary Crashes

↓

10:00:10

Failure Detected

↓

10:00:12

Replica Promoted

↓

10:00:14

Traffic Resumes
```

Downtime.

Approximately.

9 seconds.

---

# Components Involved

Automatic failover.

Usually requires.

- Health Checks
- Heartbeats
- Leader Election
- Service Discovery
- Load Balancer
- DNS Update

---

# Business Example

Online Banking.

Primary.

Fails.

A replica.

Becomes Primary.

Customers.

Continue transferring money.

Without noticing.

---

# Automatic vs Manual Failover

| Automatic | Manual |
|------------|---------|
| Fast | Slower |
| Minimal downtime | Human intervention |
| Higher complexity | Simpler |
| Preferred for production | Often used for maintenance |

---

# Real Systems

Automatic failover.

Exists in.

- PostgreSQL Patroni
- MySQL InnoDB Cluster
- MongoDB Replica Sets
- CockroachDB
- Google Spanner
- Kubernetes Stateful Applications

---

# 9.2 Failback

> **Failback is the process of returning traffic to the original primary after it has recovered.**

---

# Example

Initial state.

```text
Primary

↓

Replica
```

---

Failure.

```text
Replica

↓

New Primary
```

---

Recovery.

```text
Old Primary

↓

Replica

↓

(Optional)

Promote Back
```

---

# Important

Failback.

Should not happen.

Immediately.

The recovered server.

Must first.

Synchronize.

Missing updates.

---

# Why?

Suppose.

Primary crashed.

One hour ago.

Thousands.

Of writes.

Occurred.

During.

The outage.

Before.

Becoming Primary again.

It must.

Receive.

Every missing transaction.

---

# Synchronization

```text
Recovered Server

↓

Replication Catch-up

↓

Consistent

↓

Eligible For Promotion
```

---

# Business Perspective

Failover.

Keeps.

The business running.

Failback.

Restores.

The preferred architecture.

---

# 9.3 Split Brain

> **Split Brain occurs when multiple nodes incorrectly believe they are the Primary and both accept writes independently.**

Split Brain.

Is one of.

The most dangerous.

Failure scenarios.

In distributed systems.

---

# Example

Network partition.

Occurs.

```text
Primary

❌

Replica
```

Communication stops.

---

Primary thinks.

Replica failed.

Replica thinks.

Primary failed.

---

Both become.

Primary.

```text
Primary A

↓

Accept Writes

-------------------

Primary B

↓

Accept Writes
```

Now.

Two different.

Versions.

Of the database.

Exist.

---

# Timeline

```text
Primary

↓

Network Partition

↓

Replica Cannot Reach Primary

↓

Replica Promotes Itself

↓

Two Primaries

↓

Conflicting Writes
```

---

# Why Split Brain Is Dangerous

Customer A.

Deposits.

₹10,000.

On Primary A.

Customer B.

Withdraws.

₹10,000.

From Primary B.

Later.

Partition heals.

Question.

Which history.

Is correct?

No easy answer.

---

# Real Consequences

Split Brain.

Can cause.

- Lost writes
- Duplicate orders
- Double booking
- Financial inconsistency
- Corrupted data
- Manual recovery

---

# Preventing Split Brain

Distributed systems.

Use.

Several techniques.

---

## Leader Election

Only one node.

Can become.

Primary.

Consensus.

Determines.

The winner.

---

## Quorum

Node.

Must contact.

A majority.

Before.

Accepting writes.

---

## Fencing Tokens

Every leader.

Receives.

A unique.

Increasing token.

Old leaders.

Cannot continue.

Writing.

---

## STONITH

**Shoot The Other Node In The Head**

A real HA concept.

Used in.

Cluster software.

The old leader.

Is forcibly isolated.

Before.

A new leader.

Accepts writes.

---

# Example

```text
Old Primary

↓

Power Off

↓

New Primary

↓

Accept Writes
```

No overlap.

No split brain.

---

# Real Systems

Split Brain.

Protection.

Exists in.

- ZooKeeper
- etcd
- Kubernetes
- Pacemaker
- Corosync
- CockroachDB
- Spanner

---

# 9.4 Disaster Recovery (DR)

> **Disaster Recovery is the ability to restore services after catastrophic failures such as an entire data center or region becoming unavailable.**

Replication.

Within one region.

Is not enough.

---

# Example

Mumbai.

Data center.

Flooded.

Entire region.

Unavailable.

Question.

Can.

The application.

Continue?

---

# Multi-Region Replication

```text
Mumbai

↓

Primary

----------------------

Singapore

↓

Replica

----------------------

Frankfurt

↓

Replica
```

If.

Mumbai.

Fails.

Singapore.

Can continue.

Serving users.

---

# Disaster Recovery Goals

Every DR strategy.

Defines two metrics.

---

## RPO (Recovery Point Objective)

> **Maximum acceptable data loss.**

Example.

```text
RPO = 0
```

Means.

No data loss.

Allowed.

---

Example.

```text
RPO = 5 Minutes
```

Business accepts.

Losing.

Up to.

Five minutes.

Of data.

---

## RTO (Recovery Time Objective)

> **Maximum acceptable downtime before service is restored.**

Example.

```text
RTO = 30 Seconds
```

System.

Must recover.

Within.

Thirty seconds.

---

# RPO vs RTO

| Metric | Meaning |
|----------|----------|
| RPO | How much data can be lost? |
| RTO | How long can the system be unavailable? |

---

# Disaster Recovery Strategies

## Cold Standby

Infrastructure.

Exists.

But.

Not running.

Lowest cost.

Longest recovery.

---

## Warm Standby

Replica.

Running.

Limited traffic.

Moderate cost.

Moderate recovery.

---

## Hot Standby

Replica.

Fully synchronized.

Ready immediately.

Highest cost.

Fastest recovery.

---

# Comparison

| Strategy | Cost | Recovery Speed |
|----------|------|----------------|
| Cold Standby | Low | Slow |
| Warm Standby | Medium | Medium |
| Hot Standby | High | Fast |

---

# Business Examples

## Banking

Requirement.

```text
RPO = 0

RTO < 1 Minute
```

Use.

Synchronous replication.

Hot standby.

---

## Social Media

Requirement.

```text
RPO

Few Seconds

↓

Acceptable
```

Use.

Asynchronous replication.

Warm standby.

---

## Analytics

Requirement.

```text
Hours

Acceptable
```

Cold standby.

Often sufficient.

---

# Production Architecture

```text
                  Users

                    │

          Load Balancer

                    │

             Primary Region

              Primary DB

                    │

      ─────────Replication─────────

                    │

            Secondary Region

              Replica DB

                    │

           Automatic Failover

                    │

             Disaster Recovery
```

---

# Common Misconceptions

### Misconception 1

> Replication automatically provides disaster recovery.

False.

Replication.

Within the same data center.

Does not protect.

Against regional disasters.

---

### Misconception 2

> Failover and Failback are the same.

False.

Failover.

Handles failure.

Failback.

Restores the preferred topology.

---

### Misconception 3

> Split Brain only happens in Multi-Leader systems.

False.

It can occur.

In any replicated system.

If leader election.

Fails.

---

### Misconception 4

> Hot standby is always the best choice.

False.

It is also.

The most expensive.

The right choice.

Depends on.

Business RPO.

And.

RTO.

---

# Architect's Decision Framework

Before designing a disaster recovery strategy, ask:

| Question | Why It Matters |
|----------|----------------|
| What is the acceptable downtime (RTO)? | Determines failover requirements |
| How much data loss is acceptable (RPO)? | Influences replication strategy |
| Can the business afford a second region? | Impacts DR architecture |
| How quickly must failover occur? | Guides automation level |
| How will split brain be prevented? | Determines consensus requirements |
| Is failback automatic or manual? | Affects operational procedures |

---

# Architect's Perspective

Replication alone does not guarantee business continuity.

A resilient architecture must also answer:

- **How are failures detected?**
- **Who becomes the new leader?**
- **How are conflicting leaders prevented?**
- **What data can be lost?**
- **How quickly can the business recover?**

These questions connect replication with the next major topic in distributed systems:

**Consensus Algorithms**, which ensure that distributed nodes agree on a single leader and a single source of truth.

---

**End of Part 8**

The next part will cover:

- **Real-world Replication Implementations**
- **MySQL Replication**
- **PostgreSQL WAL Replication**
- **MongoDB Replica Sets**
- **Cassandra Replication**
- **Kafka Replication**
- **Redis Replication**
- **Architecture Comparison**
- **Chapter Summary**
