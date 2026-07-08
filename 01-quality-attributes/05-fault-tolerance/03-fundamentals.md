
# 13. Fault Tolerance Fundamentals

> **Fault tolerance begins long before recovery. A system must first detect that something has gone wrong.**

One of the biggest misconceptions is that redundancy alone provides fault tolerance.

It does not.

Suppose you have:

```text
Primary Server

↓

Replica Server
```

If the primary server fails but the system cannot detect the failure,

traffic continues flowing to the failed server.

Customers still experience outages.

Fault tolerance therefore consists of several coordinated capabilities:

1. Detect failures
2. Isolate failures
3. Redirect traffic
4. Recover automatically
5. Continue serving customers

---

# Fault Tolerance Lifecycle

```text
Fault Occurs

↓

Failure Detection

↓

Isolation

↓

Recovery Decision

↓

Failover

↓

Service Continues

↓

Repair

↓

Normal Operation
```

---

# 13.1 Failure Detection

A system cannot recover from failures it cannot detect.

Failure detection answers:

> **Has this component stopped functioning correctly?**

---

## Detection Sources

Failures may be detected through:

- Health checks
- Heartbeats
- Monitoring
- Timeouts
- Error rates
- Missing responses
- Consensus algorithms

---

# Why Fast Detection Matters

Suppose a payment service crashes.

Detection after:

```
30 Seconds
```

Customers experience:

```
30 Seconds

of failures
```

Detection after:

```
2 Seconds
```

Customer impact becomes much smaller.

Detection time directly influences business impact.

---

# Detection Challenges

Distributed systems face uncertainty.

Example:

```
API Timeout
```

Did:

- Server crash?
- Network fail?
- Packet drop?
- DNS fail?
- Slow processing?

Architects must distinguish between:

**Slow**

and

**Dead**

This is surprisingly difficult.

---

# 13.2 Health Checks

Health checks verify whether a component is operating correctly.

---

## Basic Health Check

Example:

```
GET /health
```

Response:

```json
{
  "status":"UP"
}
```

---

## Deep Health Check

Basic checks only verify process availability.

Deep health checks verify dependencies.

Example:

```text
Application

↓

Database

↓

Redis

↓

Kafka

↓

External APIs
```

Only when critical dependencies are healthy should the application report:

```
Healthy
```

---

# Types of Health Checks

| Type | Purpose |
|------|----------|
| Liveness | Is the process alive? |
| Readiness | Can it receive traffic? |
| Startup | Has initialization completed? |
| Dependency | Are downstream services available? |

---

# Kubernetes Example

Kubernetes continuously evaluates:

```yaml
livenessProbe

readinessProbe

startupProbe
```

Each probe serves a different purpose.

---

# Liveness Probe

Question:

```
Is application running?
```

Failure action:

```
Restart Container
```

---

# Readiness Probe

Question:

```
Can application receive traffic?
```

Failure action:

```
Remove From Load Balancer
```

---

# Startup Probe

Question:

```
Has application finished initialization?
```

Useful for:

- JVM startup
- Large applications
- Slow initialization

---

# 13.3 Heartbeats

Heartbeats are periodic signals indicating a component is alive.

Example:

```
Server

↓

Heartbeat Every

5 Seconds
```

---

If heartbeats stop:

```
Failure Suspected
```

---

# Heartbeat Architecture

```text
Primary

────Heartbeat────>

Monitor

────Heartbeat────>

Replica
```

Missing heartbeats trigger recovery procedures.

---

# Heartbeat Trade-offs

Short interval:

Advantages

- Faster detection

Disadvantages

- More network traffic

---

Long interval:

Advantages

- Lower overhead

Disadvantages

- Slower recovery

Architects choose intervals based on business requirements.

---

# 13.4 Watchdogs

A watchdog continuously monitors another process.

If the monitored process becomes unresponsive,

the watchdog initiates recovery.

---

Example

```text
Application

↓

Stops Responding

↓

Watchdog

↓

Restart
```

---

Common Uses

- Embedded systems
- Industrial automation
- Kubernetes
- Databases
- Distributed systems

---

# 13.5 Timeouts

Waiting forever is never fault tolerant.

Every remote operation should define a timeout.

---

Bad Design

```text
Request

↓

Wait Forever
```

---

Good Design

```text
Request

↓

3 Seconds

↓

Timeout

↓

Fallback
```

---

# Timeout Types

Examples include:

- Connection timeout
- Read timeout
- Write timeout
- Database timeout
- API timeout

---

# Why Timeouts Matter

Without timeouts:

- Threads remain blocked
- Connection pools exhaust
- Cascading failures begin

Timeouts protect the rest of the system.

---

# Timeout Budget

Suppose overall SLA:

```
2 Seconds
```

Allocate:

| Component | Timeout |
|-----------|---------:|
| API Gateway | 100 ms |
| Application | 500 ms |
| Database | 700 ms |
| External API | 500 ms |
| Buffer | 200 ms |

Every dependency receives an explicit timeout.

---

# 13.6 Retries

Failures are sometimes temporary.

Retrying may succeed.

Example:

```
Request

↓

Timeout

↓

Retry

↓

Success
```

---

# Suitable Retry Scenarios

- Temporary network issues
- Short-lived service overload
- Transient database errors
- Message broker interruptions

---

# Unsuitable Retry Scenarios

Avoid retries for:

- Invalid input
- Authentication failures
- Permission errors
- Business validation failures

Retries waste resources when the failure is permanent.

---

# Retry Strategies

## Immediate Retry

```text
Fail

↓

Retry Immediately
```

Useful only for rare situations.

---

## Fixed Delay

```text
Retry

↓

2 Seconds

↓

Retry
```

Simple.

May create synchronized retry storms.

---

## Exponential Backoff

```text
1 Second

↓

2 Seconds

↓

4 Seconds

↓

8 Seconds
```

Reduces system pressure.

Preferred in distributed systems.

---

## Exponential Backoff with Jitter

Randomize retry intervals.

Example:

```
2.3 Seconds

↓

4.7 Seconds

↓

9.2 Seconds
```

This prevents thousands of clients retrying simultaneously.

---

# Retry Decision Matrix

| Failure | Retry? |
|----------|---------|
| Network Timeout | Yes |
| HTTP 503 | Yes |
| Database Connection Lost | Yes |
| HTTP 400 | No |
| Authentication Failure | No |
| Validation Error | No |

---

# 13.7 Failure Isolation

A fault-tolerant architecture prevents one failure from spreading.

Without isolation:

```text
Service A

↓

Service B

↓

Service C

↓

Entire Platform Slows
```

---

With isolation:

```text
Service B

↓

Failure

↓

Contained

↓

A and C Continue
```

Isolation prevents cascading failures.

---

# Isolation Techniques

Examples include:

- Bulkheads
- Thread pools
- Resource quotas
- Circuit breakers
- Queue separation

These mechanisms limit failure propagation.

---

# 13.8 Graceful Degradation

Sometimes complete functionality is impossible.

Instead of failing completely,

provide reduced functionality.

---

Example

Shopping Website

Recommendation Service fails.

Instead of:

```
500 Error
```

Display:

```
Popular Products
```

Customers continue shopping.

---

# More Examples

Video Streaming

↓

Lower Video Resolution

instead of

↓

Stopping Playback

---

AI Assistant

↓

Shorter Response

instead of

↓

Unavailable

---

Maps

↓

Cached Map Tiles

instead of

↓

Blank Screen

---

# Benefits

Graceful degradation:

- Improves customer experience
- Protects revenue
- Reduces visible outages

---

# 13.9 Redundancy

Fault tolerance requires redundant resources.

Without redundancy:

```
One Server

↓

Failure

↓

Downtime
```

---

With redundancy:

```
Server A

Server B

↓

Traffic Continues
```

---

# Types of Redundancy

| Type | Example |
|------|----------|
| Hardware | Multiple Servers |
| Network | Multiple Routers |
| Storage | RAID |
| Database | Replicas |
| Region | Multi-region Deployment |
| Application | Multiple Instances |

---

# Cost of Redundancy

Redundancy increases:

- Infrastructure cost
- Operational complexity
- Synchronization effort

Architects balance redundancy against business risk.

---

# 13.10 Replication

Replication creates additional copies of data or services.

---

## Data Replication

```text
Primary Database

↓

Replica A

↓

Replica B
```

---

## Service Replication

```text
Load Balancer

↓

Instance A

Instance B

Instance C
```

---

# Replication Goals

- Improve availability
- Enable failover
- Reduce downtime
- Increase resilience

---

# Synchronous Replication

Primary waits until replicas acknowledge.

Advantages:

- Strong consistency

Disadvantages:

- Higher latency

---

# Asynchronous Replication

Primary responds immediately.

Replication occurs later.

Advantages:

- Better performance

Disadvantages:

- Possible data loss during failures

---

# Comparison

| Synchronous | Asynchronous |
|-------------|--------------|
| Strong Consistency | Better Performance |
| Higher Latency | Eventual Consistency |
| Lower Data Loss | Faster Writes |

---

# Architect's Perspective

Fault tolerance is not achieved through a single technology.

It is achieved by combining:

- Fast failure detection
- Health monitoring
- Heartbeats
- Timeouts
- Intelligent retries
- Failure isolation
- Graceful degradation
- Redundancy
- Replication

Together, these mechanisms enable systems to continue delivering business value even when failures occur.

---

**End of Part 3**

The next section will cover **Architecture Decisions**, including:

- Active-Active vs Active-Passive
- Automatic Failover
- Leader Election
- Quorum
- Consensus (Raft/Paxos Overview)
- Circuit Breaker Integration
- Bulkhead Isolation
- Retry + Timeout Design
- Dead Letter Queues
- Idempotency
- Transaction Recovery
- Fault Tolerance Decision Matrix
