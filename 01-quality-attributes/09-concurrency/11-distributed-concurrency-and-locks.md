
# 63. Distributed Concurrency

> **Concurrency becomes significantly more complex when multiple machines, rather than multiple threads, access the same shared resource.**

Inside a single JVM,

multiple threads share memory.

Across multiple servers,

memory is no longer shared.

Instead, systems communicate through:

- Networks
- Databases
- Distributed caches
- Message brokers

New challenges emerge.

---

# Why Distributed Concurrency Exists

Imagine:

```
Inventory Service

Replicas = 10
```

```text
Pod 1

↓

Inventory

----------------

Pod 2

↓

Inventory

----------------

Pod 3

↓

Inventory

...

↓

Pod 10
```

All replicas receive customer requests simultaneously.

Question:

How do we ensure only one replica updates inventory correctly?

Traditional Java locks no longer work.

---

# Local Lock Problem

Suppose:

```java
synchronized(lock){

    processOrder();

}
```

Application deployed on:

```
10 Kubernetes Pods
```

Each pod has:

Its own JVM.

Its own memory.

Its own lock.

Result:

Ten different locks.

No coordination.

---

# Architecture

```text
Pod A

↓

Lock A

-------------------

Pod B

↓

Lock B

-------------------

Pod C

↓

Lock C
```

Each lock protects only its own process.

Business data remains vulnerable.

---

# Distributed Lock

Instead,

lock is stored in a shared coordination system.

```text
Pod A

↓

Distributed Lock

↑

Pod B

↑

Pod C
```

Only one pod acquires ownership.

---

# Distributed Concurrency Problems

Distributed systems introduce entirely new challenges.

Examples:

- Network latency
- Clock differences
- Node failures
- Split-brain
- Partial failures
- Network partitions

Traditional synchronization mechanisms cannot solve these.

---

# Examples

Distributed concurrency appears in:

- Leader Election
- Inventory Reservation
- Payment Processing
- Job Scheduling
- Distributed Cron Jobs
- Kafka Consumer Groups
- Kubernetes Controllers

---

# 64. Distributed Locks

> **A Distributed Lock guarantees that only one distributed process can execute a critical section at a time.**

Unlike Java synchronization,

distributed locks coordinate:

Multiple servers.

---

# Example

Suppose:

Inventory:

```
1 Laptop
```

Requests arrive:

```text
Server A

↓

Buy Laptop

-----------------

Server B

↓

Buy Laptop

-----------------

Server C

↓

Buy Laptop
```

Distributed lock ensures:

Only one server proceeds.

---

# Workflow

```text
Acquire Lock

↓

Business Logic

↓

Release Lock
```

If lock unavailable:

Wait.

Retry.

Or fail.

---

# Characteristics

Good distributed locks should provide:

- Mutual Exclusion
- Fault Recovery
- Timeout
- Lease Expiration
- High Availability

---

# Lock Lifecycle

```text
Acquire

↓

Execute

↓

Renew (Optional)

↓

Release

or

Expire
```

---

# Why Leases Matter

Suppose:

Server crashes.

Never releases lock.

Without timeout:

Entire system blocked forever.

Lease automatically expires.

Another server continues.

---

# Lock Timeout

```text
Acquire

↓

30 Seconds

↓

Auto Expire
```

Prevents permanent deadlocks.

---

# Challenges

Distributed locks introduce:

- Network failures
- Clock skew
- Lock expiration
- Duplicate ownership
- Recovery complexity

---

# 65. Redis Distributed Lock

Redis is commonly used because:

- Extremely fast
- Simple API
- Low latency
- Widely deployed

---

# Basic Algorithm

Acquire lock:

```text
SET

Key

Value

NX

PX 30000
```

Meaning:

```
Create key

Only If Not Exists

Expire After 30 Seconds
```

---

# Example

```text
inventory-lock

↓

Owner

↓

Server-5

↓

Expires

↓

30 Seconds
```

---

# Release

Server verifies:

```
I Own Lock
```

Then deletes key.

Ownership verification prevents one server from accidentally deleting another server's lock.

---

# Safe Unlock

Typical flow:

```text
Acquire Lock

↓

Unique Owner ID

↓

Business Logic

↓

Verify Owner

↓

Delete Lock
```

Owner verification is critical.

---

# Problem

Suppose:

Lock expires.

Another server acquires it.

Original server later deletes the key.

Now:

Second server's lock disappears.

Incorrect.

Ownership verification avoids this.

---

# Redis Lock Timeline

```text
Server A

Acquire

↓

Execute

↓

Release

-----------------

Server B

Waiting

↓

Acquire
```

---

# Advantages

- Simple
- Fast
- Easy implementation

---

# Limitations

Single Redis instance:

Single point of failure.

Replication introduces consistency challenges.

---

# 66. Redlock

Redis creator proposed:

```
Redlock
```

for higher availability.

---

# Architecture

```text
Redis 1

Redis 2

Redis 3

Redis 4

Redis 5
```

Client attempts:

Majority acquisition.

Example:

Acquire:

```
3 of 5
```

Lock considered successful.

---

# Goal

Avoid:

Single Redis failure.

---

# Algorithm

Acquire lock on:

Multiple Redis servers.

If majority succeeds within lease duration:

Lock granted.

Otherwise:

Release acquired locks.

Retry.

---

# Benefits

- Higher availability
- Better fault tolerance
- Survives individual Redis failures

---

# Important Discussion

Redlock is **controversial** in the distributed systems community.

Some researchers argue it is insufficient under certain network partition and timing assumptions for applications requiring very strong correctness guarantees.

Many production systems instead prefer coordination services such as:

- ZooKeeper
- etcd
- Consul

when strict coordination is required.

---

# When Redis Lock Is Good

Suitable for:

- Background jobs
- Scheduled tasks
- Cache refresh
- Non-critical coordination

---

# When Redis Lock Is Not Ideal

Avoid for:

- Financial transfers
- Distributed consensus
- Critical leader election
- Strong consistency requirements

---

# 67. ZooKeeper

Apache ZooKeeper is a distributed coordination service.

Purpose:

Coordinate distributed systems.

Not store business data.

---

# Capabilities

ZooKeeper provides:

- Distributed Locks
- Leader Election
- Configuration
- Naming
- Service Discovery
- Coordination

---

# Architecture

```text
Application

↓

ZooKeeper Cluster

↓

Coordination
```

Applications coordinate through ZooKeeper.

---

# ZNodes

ZooKeeper stores:

```
ZNodes
```

Small coordination objects.

Not business records.

---

# Ephemeral Nodes

Unique feature.

Suppose:

Server acquires lock.

Creates:

Ephemeral Node.

Server crashes.

Node automatically disappears.

Lock released.

Excellent failure recovery.

---

# Sequential Nodes

ZooKeeper supports:

```
Sequential Nodes
```

Useful for:

Queues.

Leader election.

Ordering.

---

# Leader Election Example

Servers:

```
A

B

C

D
```

Each creates:

Sequential node.

Smallest sequence:

Leader.

If leader crashes:

Next smallest becomes leader.

---

# Advantages

- Strong consistency
- Reliable coordination
- Automatic recovery
- Proven production technology

---

# Disadvantages

- Operational complexity
- Requires cluster
- Higher latency than Redis

---

# 68. etcd

etcd is a distributed key-value store used extensively in cloud-native platforms.

Most famous user:

```
Kubernetes
```

---

# Responsibilities

etcd stores:

- Cluster State
- Configuration
- Leader Information
- Distributed Locks
- Coordination Metadata

---

# Kubernetes Example

Control Plane.

```text
API Server

↓

etcd

↓

Cluster State
```

Every Kubernetes object ultimately persists in etcd.

---

# Features

- Strong consistency
- Watches
- Leases
- Transactions
- Leader election

---

# Lease

etcd provides:

Lease-based ownership.

If process dies:

Lease expires.

Resources released automatically.

---

# Comparison

| Redis | ZooKeeper | etcd |
|--------|-----------|------|
| Fast | Strong Coordination | Cloud Native |
| Cache | Coordination | Coordination |
| Eventual Availability Focus | Strong Consistency | Strong Consistency |
| Simple | Complex | Medium |

---

# 69. Leader Election

> **Leader Election ensures that exactly one node performs a specific responsibility at any point in time.**

Examples:

- Scheduler
- Controller
- Primary Database
- Kafka Controller
- Kubernetes Controller Manager

---

# Why Needed

Suppose:

Five servers execute:

```
Daily Billing
```

Without leader election:

Customers billed:

```
5 Times
```

---

# Workflow

```text
Server A

↓

Leader

----------------

Server B

Follower

----------------

Server C

Follower
```

Only leader performs work.

---

# Leader Failure

Leader crashes.

Followers detect failure.

Election begins.

New leader selected.

---

# Requirements

Good leader election should provide:

- Exactly one leader
- Fast failover
- Split-brain prevention
- Automatic recovery

---

# Split-Brain

Very dangerous.

Suppose:

Network partition.

Both servers believe:

```
I Am Leader
```

Now:

Duplicate work.

Data corruption.

Consensus protocols prevent this.

---

# 70. Lease-Based Locks

Instead of permanent locks,

distributed systems prefer:

Leases.

---

# Why?

Server crashes.

Lock eventually expires.

Another server continues.

No manual cleanup.

---

# Lease Timeline

```text
Acquire

↓

Lease

30 Seconds

↓

Renew

↓

Renew

↓

Crash

↓

Expire

↓

New Owner
```

---

# Lease Renewal

Long-running work.

Server periodically renews lease.

If renewal stops:

Lease expires.

---

# Common Uses

- Kubernetes controllers
- Distributed schedulers
- Cluster coordination
- Job execution

---

# Distributed Lock Decision Matrix

| Requirement | Recommended Technology |
|-------------|------------------------|
| Cache Refresh | Redis Lock |
| Background Scheduler | Redis Lock |
| Kubernetes Coordination | etcd |
| Strong Leader Election | ZooKeeper / etcd |
| Financial Coordination | Consensus-based systems (ZooKeeper, etcd, etc.) |
| Cloud Native | etcd |
| Service Coordination | ZooKeeper |

---

# Common Misconceptions

### Myth

Java `synchronized` works across servers.

**Reality**

It protects only threads within the same JVM.

---

### Myth

Distributed locks are just slower mutexes.

**Reality**

They must also handle crashes, network failures, partitions, leases, and ownership verification.

---

### Myth

Redis is always the best distributed lock.

**Reality**

Redis is excellent for many coordination tasks, but not every workload requires—or is suited to—Redis-based locking.

---

### Myth

Leader election is only for databases.

**Reality**

Leader election is widely used in Kubernetes, Kafka, distributed schedulers, workflow engines, and many cloud platforms.

---

# Architect's Perspective

Distributed concurrency is fundamentally different from local concurrency.

Inside one JVM, synchronization coordinates **threads**.

Across a cluster, coordination must handle:

- Independent processes
- Machine failures
- Network partitions
- Clock differences
- Partial failures

This is why experienced architects prefer:

- Stateless services
- Idempotent operations
- Message-driven coordination
- Optimistic concurrency

over relying heavily on distributed locks.

Distributed locks should be treated as a **specialized coordination mechanism**, not a default design pattern.

Whenever possible:

> **Eliminate the need for distributed locking through better system design.**

---

**End of Part 11**

The next section will cover:

- **Java Concurrency**
- **Executor Framework**
- **Fork/Join Framework**
- **Virtual Threads (Project Loom)**
- **Spring Boot Concurrency**
- **Kafka Consumer Concurrency**
- **Kubernetes Concurrency**
- **AWS, Azure, and GCP Concurrency Services**
