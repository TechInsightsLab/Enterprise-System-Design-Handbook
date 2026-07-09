
# 14. Architecture Decisions

> **Fault tolerance is achieved through deliberate architectural decisions rather than reactive error handling.**

Many teams attempt to improve fault tolerance by simply adding retries or restarting failed services.

These techniques help.

However,

enterprise fault tolerance begins with architectural decisions made **before** the first line of code is written.

Architects ask questions such as:

- Should services be Active-Active or Active-Passive?
- Should failures trigger automatic failover?
- How should split-brain scenarios be prevented?
- How can cascading failures be avoided?
- Which data requires strong consistency?
- Which failures should be hidden from customers?

These decisions determine how the system behaves during failures.

---

# Fault Tolerance Decision Framework

```text
Business Requirement

↓

Failure Analysis

↓

Identify Failure Domain

↓

Architecture Decision

↓

Implementation

↓

Testing

↓

Continuous Validation
```

Every architectural decision should reduce business impact during failures.

---

# 14.1 Active-Active Architecture

Multiple instances simultaneously serve production traffic.

```text
Users

↓

Load Balancer

↓

Service A

Service B

Service C
```

All instances remain active.

---

## Benefits

- Maximum availability
- Excellent load distribution
- Faster failover
- Better scalability

---

## Challenges

- Data synchronization
- Session management
- Conflict resolution
- Higher operational complexity

---

## Typical Use Cases

- API servers
- Web applications
- Stateless services
- CDN edge nodes

---

# 14.2 Active-Passive Architecture

Only one instance actively serves requests.

The secondary waits until failure occurs.

```text
Primary

↓

Serving Requests

-----------------

Standby

↓

Waiting
```

---

## Failover

```text
Primary

↓

Failure

↓

Standby Promoted

↓

Traffic Redirected
```

---

## Benefits

- Simpler design
- Easier consistency
- Predictable recovery

---

## Challenges

- Standby resources remain underutilized
- Slightly longer failover
- Capacity planning required

---

## Typical Use Cases

- Databases
- Legacy applications
- Financial systems

---

# Active-Active vs Active-Passive

| Active-Active | Active-Passive |
|---------------|----------------|
| All nodes serve traffic | One active node |
| Better utilization | Simpler architecture |
| Faster failover | Easier consistency |
| Higher complexity | Lower infrastructure efficiency |

---

# Decision Guidelines

Use Active-Active when:

- Stateless services
- High scalability
- Low latency failover

Use Active-Passive when:

- Strong consistency
- Stateful workloads
- Simpler operational model

---

# 14.3 Automatic Failover

Manual recovery is slow.

Fault-tolerant systems automate failover.

---

## Example

```text
Primary Database

↓

Failure

↓

Health Check

↓

Replica Promoted

↓

Traffic Updated
```

Customers continue working.

---

## Failover Steps

1. Detect failure
2. Confirm failure
3. Elect replacement
4. Redirect traffic
5. Resume service

---

## Benefits

- Lower MTTR
- Reduced customer impact
- Less operational effort

---

## Risks

Incorrect failure detection may promote healthy replicas unnecessarily.

False positives are dangerous.

---

# 14.4 Leader Election

Certain systems require one node to coordinate activities.

Examples:

- Scheduler
- Primary Database
- Metadata Service
- Distributed Lock Manager

---

## Example

```text
Cluster

↓

Node A

Leader

↓

Node B

Follower

↓

Node C

Follower
```

---

## Leader Failure

```text
Leader

↓

Failure

↓

Election

↓

New Leader
```

Operations continue.

---

## Election Requirements

Leader election should be:

- Automatic
- Fast
- Deterministic
- Consistent

---

# Common Leader Election Technologies

Examples include:

- Raft
- Paxos
- ZooKeeper
- etcd
- Consul

---

# 14.5 Quorum

Distributed systems often require agreement among multiple nodes.

Quorum defines:

> **Minimum number of participants required before accepting an operation.**

---

## Example

Five Nodes

```
A

B

C

D

E
```

Majority:

```
3
```

Operations require agreement from at least three nodes.

---

# Why Quorum Matters

Without quorum,

different parts of the system may accept conflicting updates.

---

# Read and Write Quorum

Example:

```
N = 5

Write Quorum = 3

Read Quorum = 3
```

Ensures overlap between reads and writes.

---

# 14.6 Consensus

Consensus algorithms ensure distributed systems agree on shared state despite failures.

---

## Goals

- One leader
- Consistent decisions
- Safe failover
- No conflicting updates

---

# Raft (High Level)

Simplified flow:

```text
Leader

↓

Replicate Log

↓

Followers

↓

Majority Ack

↓

Commit
```

---

## Characteristics

- Easier to understand
- Widely adopted
- Excellent for distributed coordination

---

# Paxos (High Level)

Another consensus algorithm.

Provides:

- Safety
- Fault tolerance
- Distributed agreement

More complex than Raft.

---

# When Architects Need Consensus

Examples:

- Distributed databases
- Kubernetes control plane
- Metadata services
- Configuration management

---

# 14.7 Circuit Breaker Integration

Repeated retries against failing services increase outages.

Circuit Breaker prevents this.

---

## Closed

```
Requests

↓

Service
```

---

## Open

```
Service Failed

↓

Reject Requests

↓

Fallback
```

---

## Half-Open

```
Test Request

↓

Healthy?

↓

Close Circuit
```

---

# Benefits

- Prevent cascading failures
- Protect downstream services
- Reduce recovery time

---

# 14.8 Bulkhead Isolation

Ships use bulkheads to prevent flooding from spreading.

Software applies the same principle.

---

## Without Bulkhead

```text
API

↓

Shared Thread Pool

↓

Failure

↓

Everything Slows
```

---

## With Bulkhead

```text
Orders

↓

Dedicated Pool

Inventory

↓

Dedicated Pool

Payments

↓

Dedicated Pool
```

One workload cannot consume all resources.

---

# 14.9 Retry + Timeout Strategy

Retries without timeouts are dangerous.

Recommended flow:

```text
Request

↓

Timeout

↓

Retry

↓

Backoff

↓

Fallback
```

---

## Best Practices

- Exponential Backoff
- Random Jitter
- Maximum Retry Count
- Idempotent Operations

---

# Retry Budget

Limit retries.

Example:

```
Maximum

3 Attempts
```

Unlimited retries amplify failures.

---

# 14.10 Dead Letter Queue (DLQ)

Some messages cannot be processed successfully.

Instead of retrying forever:

```text
Message

↓

Processing Failed

↓

Retry

↓

Retry

↓

Dead Letter Queue
```

---

## Benefits

- Prevent endless retries
- Preserve failed messages
- Enable investigation

---

## Typical Use Cases

- Kafka
- RabbitMQ
- AWS SQS
- Azure Service Bus

---

# 14.11 Idempotency

Retries may execute operations multiple times.

Idempotency ensures repeated execution produces the same business result.

---

## Example

Payment Request

```
Request ID

12345
```

Duplicate request:

```
12345
```

System recognizes duplicate.

Only one payment is processed.

---

# Typical Idempotent Operations

- Payments
- Order creation
- Inventory reservation
- Money transfer

---

# 14.12 Transaction Recovery

Distributed transactions may fail midway.

Example:

```text
Payment

↓

Success

Inventory

↓

Failed
```

Recovery mechanisms include:

- Compensation
- Retry
- Manual review
- Saga pattern

---

# Recovery Workflow

```text
Failure

↓

Detect

↓

Rollback

↓

Compensate

↓

Notify
```

---

# 14.13 Multi-Region Deployment

Critical systems survive regional failures.

```text
Users

↓

Global Load Balancer

↓

Region A

Region B

Region C
```

---

## Benefits

- Disaster tolerance
- Geographic redundancy
- Lower latency
- Business continuity

---

## Challenges

- Data consistency
- Replication
- Cost
- Operational complexity

---

# 14.14 Fault Injection

Architects intentionally introduce failures.

Examples:

- Kill containers
- Disconnect databases
- Delay network
- Simulate packet loss
- Terminate nodes

This validates recovery procedures.

---

# Popular Tools

- Chaos Monkey
- LitmusChaos
- Gremlin
- Chaos Mesh

---

# 14.15 Architecture Decision Matrix

| Failure Scenario | Recommended Decision |
|------------------|----------------------|
| Single Server Failure | Active-Active |
| Database Failure | Automatic Failover |
| Leader Failure | Leader Election |
| Network Partition | Quorum + Consensus |
| Slow Dependency | Circuit Breaker |
| Thread Exhaustion | Bulkhead |
| Temporary Failure | Retry + Backoff |
| Poison Message | Dead Letter Queue |
| Duplicate Request | Idempotency |
| Regional Failure | Multi-region Deployment |

---

# Architecture Decision Summary

| Decision | Primary Benefit | Trade-off |
|-----------|-----------------|-----------|
| Active-Active | Fast failover | Higher complexity |
| Active-Passive | Simplicity | Idle capacity |
| Automatic Failover | Reduced downtime | False failover risk |
| Leader Election | Consistency | Election overhead |
| Quorum | Safe distributed decisions | Increased latency |
| Circuit Breaker | Failure containment | Additional state management |
| Bulkhead | Failure isolation | Resource fragmentation |
| Retry + Timeout | Recovery from transient faults | Retry storms if misconfigured |
| Dead Letter Queue | Preserve failed messages | Operational management |
| Idempotency | Safe retries | Additional storage/logic |
| Multi-region | Disaster tolerance | Cost and consistency challenges |

---

# Architect's Perspective

Enterprise fault tolerance is rarely provided by a single pattern.

Instead, production systems combine multiple complementary strategies.

For example, a payment platform may use:

- Active-Active API servers
- Active-Passive databases
- Automatic failover
- Health checks and heartbeats
- Circuit breakers for payment gateways
- Bulkheads for independent business domains
- Retries with exponential backoff and jitter
- Dead Letter Queues for failed events
- Idempotency keys for payment processing
- Multi-region disaster protection

Each architectural decision addresses a specific failure scenario.

The goal is not to eliminate failures—it is to ensure that failures have **minimal business impact**.

---

**End of Part 4**

The next section will cover **Implementation Mechanisms**, including:

- Kubernetes fault tolerance
- Spring Boot fault tolerance patterns
- Resilience4j
- Service Mesh fault handling
- Kafka fault tolerance
- Database replication mechanisms
- Cloud-native fault tolerance (AWS, Azure, GCP)
- Monitoring and failure detection tools
- Chaos engineering platforms
- Fault tolerance implementation decision matrix
