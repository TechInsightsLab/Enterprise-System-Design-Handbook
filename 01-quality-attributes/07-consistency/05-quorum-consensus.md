
# 19. Quorum-Based Consistency

> **Quorum is one of the most practical techniques used to balance consistency, availability, and performance in distributed databases.**

Instead of requiring every replica to participate in every operation,

the system requires only a **minimum number of replicas** to agree.

This minimum is called a **quorum**.

---

# Why Quorum Exists

Imagine a distributed database with three replicas.

```text
Replica A

Replica B

Replica C
```

If every write waits for all replicas,

one slow replica delays the entire system.

Instead,

the database waits for only a majority.

---

# Basic Terminology

Suppose:

```
N = Total Replicas

R = Read Quorum

W = Write Quorum
```

Example:

```
N = 3

R = 2

W = 2
```

---

# Majority Quorum

For:

```
3 Replicas
```

Majority:

```
2
```

For:

```
5 Replicas
```

Majority:

```
3
```

---

# Why Majority Works

Imagine:

```
Replica A

Version 5

Replica B

Version 5

Replica C

Version 4
```

Read Quorum:

```
2
```

Any majority includes at least one replica containing:

```
Version 5
```

Therefore,

latest value can be determined.

---

# Quorum Formula

Strong quorum generally requires:

```text
R + W > N
```

Example:

```
N = 3

R = 2

W = 2

↓

2 + 2 > 3

↓

True
```

Latest write cannot be missed.

---

# Examples

---

## Example 1

```
N = 3

R = 1

W = 1
```

Advantages:

- Very fast

Disadvantages:

- Weak consistency
- Stale reads likely

---

## Example 2

```
N = 3

R = 2

W = 2
```

Balanced.

Common configuration.

---

## Example 3

```
N = 5

R = 3

W = 3
```

Stronger consistency.

Higher latency.

---

# Read Quorum

Client requests data.

Database queries:

```
Replica A

Replica B
```

If values differ:

```
Version 5

Version 4
```

Newest version returned.

---

# Write Quorum

Client writes:

```
Version 6
```

Database waits until:

```
Majority

Acknowledges
```

Only then:

```
Success
```

---

# Quorum Visualization

```text
Client

↓

Write

↓

Replica A ✓

Replica B ✓

Replica C

↓

Majority

↓

Commit
```

---

# Benefits

- Better consistency
- Higher availability than synchronous replication
- Fault tolerant
- Scalable

---

# Trade-offs

- More network communication
- Higher latency
- Complex implementation

---

# Databases Using Quorum

Examples:

- Cassandra
- DynamoDB
- Riak
- ScyllaDB

---

# 20. Read Repair

Distributed replicas eventually drift apart.

Read Repair automatically corrects stale replicas during read operations.

---

# Example

Current versions:

```text
Replica A

Version 8

Replica B

Version 8

Replica C

Version 6
```

Client reads.

Database detects:

```
Replica C

Outdated
```

Database updates:

```
Replica C

↓

Version 8
```

Future reads become consistent.

---

# Read Repair Flow

```text
Read

↓

Compare Replicas

↓

Latest Version

↓

Repair Stale Replica
```

---

# Advantages

- Improves consistency automatically
- No administrator intervention
- Repairs occur naturally

---

# Limitations

Repairs happen only when reads occur.

Rarely accessed data may remain stale longer.

---

# 21. Hinted Handoff

What happens if a replica is temporarily unavailable?

Hinted handoff prevents immediate failures.

---

# Example

Three replicas.

```
A

B

C
```

Replica C unavailable.

Client writes:

```
Version 12
```

Replica A stores:

```
Version 12

+

Hint

For Replica C
```

Later,

Replica C returns.

Stored hint delivered.

Consistency restored.

---

# Flow

```text
Replica Offline

↓

Temporary Hint Stored

↓

Replica Returns

↓

Hint Delivered
```

---

# Benefits

- Better availability
- Faster recovery
- Reduced failed writes

---

# Databases

Common in:

- Cassandra
- Riak

---

# 22. Vector Clocks

Distributed systems cannot always rely on physical clocks.

Instead,

they often use logical clocks.

Vector clocks help determine:

- Which update happened first
- Whether updates conflict
- Whether one update supersedes another

---

# Example

Two replicas.

```
Replica A

Version

(2,1)

Replica B

Version

(1,2)
```

Neither dominates.

Updates occurred independently.

Conflict detected.

---

# Vector Clock Example

Customer A edits profile.

Customer B edits profile.

Both disconnected.

Later synchronization occurs.

Vector clocks identify:

```
Concurrent Updates
```

instead of incorrectly assuming one is newer.

---

# Advantages

- Conflict detection
- Distributed ordering
- No synchronized clocks required

---

# Trade-offs

- Metadata grows
- Complexity increases
- Difficult for many replicas

---

# 23. Conflict Resolution

Eventually consistent systems inevitably encounter conflicts.

Question:

Which update should survive?

---

# Example

User updates address.

US Region:

```
Address A
```

Europe Region:

```
Address B
```

Replication resumes.

Conflict exists.

---

# Conflict Resolution Strategies

---

## Last Write Wins (LWW)

Newest timestamp wins.

Advantages:

Simple.

Disadvantages:

May lose valid updates.

---

## Version Numbers

Highest version wins.

Common in:

Databases.

---

## Vector Clocks

Detect concurrent writes.

Applications decide.

---

## Merge

Combine updates.

Example:

```
Phone Number

+

Address

↓

Merged Record
```

Useful when fields do not overlap.

---

## Application Resolution

Application asks user.

Example:

Google Docs.

Git Merge.

---

# Comparison

| Strategy | Advantages | Disadvantages |
|-----------|------------|---------------|
| Last Write Wins | Simple | Data Loss |
| Version Numbers | Easy | Limited Conflict Detection |
| Vector Clocks | Accurate | Complex |
| Merge | Preserves Data | Application Logic |
| User Resolution | Correct | Manual |

---

# 24. Consensus

Consistency becomes significantly harder when nodes fail.

Consensus answers:

> **How can multiple nodes agree on one value despite failures?**

---

# Consensus Goal

Suppose:

Five servers.

Leader crashes.

Question:

Who becomes leader?

Every server must agree.

---

# Consensus Flow

```text
Proposal

↓

Voting

↓

Majority Agreement

↓

Commit

↓

Everyone Agrees
```

---

# Consensus Guarantees

Consensus algorithms ensure:

- One leader
- One decision
- No split-brain
- Ordered operations

---

# Common Algorithms

- Paxos
- Raft
- Zab
- Viewstamped Replication

---

# Where Consensus Is Used

Examples:

- etcd
- ZooKeeper
- Consul
- Google Spanner
- CockroachDB

---

# 25. Raft Overview

Raft was designed to be easier to understand than Paxos.

---

# Server States

Every node exists in one state.

```text
Follower

↓

Candidate

↓

Leader
```

---

# Leader Election

Follower stops receiving heartbeat.

↓

Election begins.

↓

Candidate requests votes.

↓

Majority votes.

↓

Leader elected.

---

# Normal Operation

```text
Client

↓

Leader

↓

Followers

↓

Commit
```

Leader coordinates updates.

---

# Benefits

- Easier implementation
- Strong consistency
- Predictable behavior

---

# 26. Paxos Overview

Paxos also provides distributed consensus.

However,

it is mathematically elegant but operationally difficult.

---

# High-Level Steps

```text
Prepare

↓

Promise

↓

Accept

↓

Accepted

↓

Commit
```

---

# Comparison

| Raft | Paxos |
|-------|--------|
| Easier to understand | More theoretical |
| Leader-based | General consensus protocol |
| Popular in modern systems | Academic foundation |
| Widely implemented | Less intuitive |

---

# 27. Leader Election

Leader failure is inevitable.

Election ensures continuity.

---

# Election Flow

```text
Leader Fails

↓

Followers Detect Timeout

↓

Election

↓

Majority Vote

↓

New Leader

↓

Service Continues
```

---

# Requirements

Good leader election should guarantee:

- One leader
- Fast recovery
- No split-brain

---

# 28. Split-Brain

One of the most dangerous distributed failures.

Definition:

Multiple leaders simultaneously believe they are primary.

---

# Example

```text
Network Partition

↓

Leader A

Leader B
```

Both accept writes.

Both diverge.

Recovery becomes extremely difficult.

---

# Consequences

- Data corruption
- Duplicate writes
- Lost updates
- Inconsistent replicas

---

# Prevention

Use:

- Consensus
- Quorum
- Fencing tokens
- Reliable leader election

---

# Split-Brain Example

```text
Before

Leader

↓

Followers

----------------

After Partition

Leader A

×

Leader B
```

Consensus prevents this situation.

---

# 29. Distributed Coordination

Large distributed systems require coordination.

Examples:

- Leader election
- Configuration
- Locks
- Membership
- Service discovery

---

# Coordination Platforms

| Technology | Primary Use |
|------------|-------------|
| ZooKeeper | Coordination |
| etcd | Kubernetes Control Plane |
| Consul | Service Discovery |
| Chubby | Google's Coordination Service |

---

# Architect's Perspective

Distributed consistency is not achieved through a single feature.

Production-grade systems combine:

- Quorum
- Replication
- Read Repair
- Hinted Handoff
- Conflict Resolution
- Consensus
- Leader Election
- Distributed Coordination

Each mechanism solves a different part of the consistency problem.

Choosing the right combination depends on:

- Business correctness requirements
- Latency expectations
- Failure tolerance
- Operational complexity

---

## Technology Comparison

| Technology | Replication | Quorum | Consensus | Conflict Resolution |
|------------|-------------|---------|-----------|---------------------|
| PostgreSQL | Leader-Based | No | No | Leader Controls Writes |
| Cassandra | Leaderless | Yes | No | Last Write Wins / Repair |
| DynamoDB | Leaderless | Yes | Internal | Versioning & Conditional Writes |
| CockroachDB | Leader-Based | Yes | Raft | Consensus-Based |
| Google Spanner | Leader-Based | Yes | Paxos | Consensus-Based |

---

**End of Part 5**

The next section will cover:

- **Strong vs Eventual Consistency in Practice**
- **Relational vs NoSQL Consistency**
- **Consistency in Kafka**
- **Consistency in Redis**
- **Consistency in Spring Boot Applications**
- **Consistency in Kubernetes**
- **Cloud-Native Consistency (AWS, Azure, GCP)**
- **Real Production Implementations**
