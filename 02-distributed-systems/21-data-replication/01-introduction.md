# Chapter 21 — Data Replication

> **Part II – Distributed Systems**

---

# Data Replication

| Attribute | Value |
|-----------|--------|
| **Version** | 2.0 |
| **Part** | II – Distributed Systems |
| **Chapter** | 21 |
| **Reading Time** | ~700–900 Minutes |
| **Difficulty** | Advanced |
| **Prerequisites** | Chapter 18 – Distributed Systems Fundamentals<br>Chapter 19 – Time, Clocks & Ordering<br>Chapter 20 – CAP Theorem & PACELC |

---

> [!NOTE]
> **Replication is the foundation of almost every distributed system.**
>
> High Availability.
>
> Fault Tolerance.
>
> Disaster Recovery.
>
> Scalability.
>
> Global Applications.
>
> None of these are possible without replication.

---

# Why This Chapter Exists

Imagine.

Your company has.

One database.

Running.

On one server.

Everything works.

Until.

The server crashes.

```text
Application

↓

Database

↓

💥 Server Failure
```

Now.

Your application.

Cannot serve users.

Business stops.

Revenue stops.

Customers leave.

---

How do we solve this?

By creating.

Additional copies.

Of the same data.

On multiple machines.

This concept is called.

**Replication.**

---

# Business Story

Imagine.

Amazon.

Stores.

Product information.

On.

One database.

```text
Laptop

₹75,000

Stock = 10
```

Millions of users.

Around the world.

Request.

The same product.

Should every request.

Travel.

To one server?

No.

It would become.

A bottleneck.

---

Instead.

Amazon keeps.

Multiple copies.

```text
US

↓

Product DB

----------------------

Europe

↓

Product DB

----------------------

India

↓

Product DB
```

Users.

Read.

From the nearest copy.

Faster.

Safer.

More reliable.

---

# Another Business Story

Consider.

A banking application.

```text
Primary Database

↓

Mumbai
```

If.

Mumbai.

Experiences.

A power outage.

Should.

The entire bank.

Stop operating?

No.

---

Instead.

The bank maintains.

Another copy.

```text
Mumbai

↓

Primary

----------------------

Hyderabad

↓

Replica
```

If one.

Fails.

The other.

Can continue.

Serving customers.

---

# What is Data Replication?

> **Data Replication is the process of maintaining multiple copies of the same data across different machines, servers, data centers, or geographic regions.**

The goal.

Is simple.

```text
One Copy

↓

Multiple Copies

↓

Same Data
```

---

# Simple Architecture

Without Replication.

```text
Users

↓

Application

↓

Database
```

---

With Replication.

```text
Users

↓

Application

↓

Primary Database

↓

Replica 1

↓

Replica 2

↓

Replica 3
```

Now.

Failure of one server.

Does not stop.

The application.

---

# Why Replication Exists

Replication solves.

Many business problems.

Not just.

High Availability.

---

## Problem 1

Single Point of Failure.

```text
One Database

↓

Failure

↓

Entire System Down
```

Replication.

Removes.

The single point.

Of failure.

---

## Problem 2

Read Scalability.

Millions.

Of customers.

Cannot read.

From one database.

Replication.

Allows.

Read traffic.

To be shared.

Across replicas.

---

## Problem 3

Disaster Recovery.

One region.

May fail.

Another region.

Continues serving.

Customers.

---

## Problem 4

Low Latency.

Customer.

In India.

Should not.

Read data.

From.

Virginia.

Every request.

---

Replication.

Places data.

Closer.

To users.

---

## Problem 5

Maintenance.

Administrators.

Need.

To upgrade servers.

Without downtime.

Replication.

Allows.

One replica.

To be upgraded.

While others.

Continue serving.

---

# Replication Goals

Replication is designed.

To achieve.

Several architectural goals.

```text
Replication

│

├── High Availability

├── Fault Tolerance

├── Disaster Recovery

├── Read Scalability

├── Low Latency

├── Global Distribution

├── Business Continuity

└── Data Durability
```

---

# High Availability

If one server fails.

Another.

Can continue.

Serving requests.

---

# Fault Tolerance

Hardware fails.

Disks fail.

VMs crash.

Replication.

Keeps.

The application running.

---

# Read Scalability

Instead of.

One database.

Serving.

One million reads.

Five replicas.

Can each serve.

Two hundred thousand.

Reads.

---

# Disaster Recovery

Entire region.

May become unavailable.

Replication.

Across regions.

Allows recovery.

Without.

Permanent data loss.

---

# Low Latency

Customer.

In Japan.

Reads.

From Japan.

Customer.

In Europe.

Reads.

From Europe.

Nearby replicas.

Reduce latency.

---

# Data Durability

If one disk.

Fails.

Copies.

Still exist.

Elsewhere.

Reducing.

Risk of data loss.

---

# Business Benefits

Replication improves.

| Business Goal | Benefit |
|---------------|----------|
| Availability | Less downtime |
| Customer Experience | Faster responses |
| Revenue | Fewer outages |
| Reliability | Data survives failures |
| Global Expansion | Serve users locally |
| Operations | Easier maintenance |

---

# Where Replication Is Used

Almost every.

Modern distributed system.

Uses replication.

Examples.

- MySQL
- PostgreSQL
- MongoDB
- Cassandra
- CockroachDB
- Google Spanner
- Kafka
- Redis
- Elasticsearch
- HBase
- DynamoDB

Replication.

Is everywhere.

---

# Replication vs Backup

These two.

Are often confused.

They solve.

Different problems.

---

## Replication

Purpose.

Keep systems.

Running.

---

Example.

```text
Primary

↓

Replica

↓

Replica
```

Live copies.

Continuously synchronized.

---

## Backup

Purpose.

Recover.

Deleted.

Or corrupted data.

---

Example.

```text
Database

↓

Daily Backup

↓

Cloud Storage
```

Backups.

Are not.

Continuously updated.

---

# Comparison

| Replication | Backup |
|-------------|---------|
| Live copies | Point-in-time copy |
| High Availability | Disaster Recovery |
| Fast failover | Restore after failure |
| Continuously synchronized | Scheduled |
| Used for production traffic | Not used for production reads |

---

# Common Misconception

Many engineers believe.

> "Replication is a backup."

False.

If.

Application deletes.

A table.

Replication.

Immediately copies.

The deletion.

To every replica.

Nothing remains.

Only.

Backups.

Protect.

Against.

Accidental deletion.

---

# Replication Challenges

Replication.

Introduces.

New problems.

```text
Replication

↓

Consistency

↓

Conflicts

↓

Replication Lag

↓

Network Partitions

↓

Split Brain

↓

Conflict Resolution
```

Ironically.

Making systems.

More reliable.

Also makes them.

More complex.

---

# Example

Customer updates.

Address.

```text
Primary

↓

Replica A

↓

Replica B
```

Question.

When should.

Replica B.

Receive the update?

Immediately?

Later?

What if.

The network fails?

These questions.

Define.

Replication strategies.

---

# Learning Objectives

By the end.

Of this chapter.

You should understand.

- Why replication exists.
- Replication architectures.
- Synchronous replication.
- Asynchronous replication.
- Semi-synchronous replication.
- Primary–Replica replication.
- Multi-Leader replication.
- Leaderless replication.
- Read replicas.
- Replication lag.
- Read-after-write consistency.
- Conflict resolution.
- Quorum replication.
- Anti-Entropy.
- Read Repair.
- Split Brain.
- Database implementations.
- Production best practices.

---

# Common Misconceptions

### Misconception 1

> Replication is the same as backup.

False.

Replication.

Improves availability.

Backup.

Protects.

Against data loss.

---

### Misconception 2

> More replicas always improve performance.

False.

Reads usually improve.

Writes often become.

More expensive.

Because replicas.

Must synchronize.

---

### Misconception 3

> Replication guarantees zero data loss.

False.

It depends on.

The replication strategy.

Especially.

Synchronous.

Versus.

Asynchronous.

---

### Misconception 4

> Replicas are always identical.

False.

Asynchronous replication.

Can produce.

Temporary differences.

Called.

Replication Lag.

---

# Architect's Perspective

Replication is not simply about creating copies of data.

It is about balancing multiple competing goals:

- Availability
- Consistency
- Latency
- Throughput
- Cost
- Disaster recovery
- Operational simplicity

Every replication strategy makes different trade-offs.

Choosing the right one depends entirely on the business requirements.

This chapter will explore those strategies in depth and explain why modern databases—from MySQL to Cassandra to Google Spanner—implement replication in very different ways.

---

**End of Part 1**

The next part will cover:

- **Replication Models**
- **Leader–Follower (Primary–Replica) Replication**
- **Leaderless Replication**
- **Multi-Leader Replication**
- **Replication Topologies**
- **Read Replicas**
- **Replication Architecture**
