# 16. Trade-offs

> **Every architectural decision improves one aspect of the system while introducing cost, complexity, or operational challenges elsewhere.**

One of the biggest differences between a junior engineer and an experienced architect is how they think about trade-offs.

A junior engineer often asks:

> "Which solution is the best?"

An architect asks:

> "Which solution is the best for this business, given its constraints?"

High Availability is no exception.

Increasing availability generally requires additional:

* Infrastructure
* Engineering effort
* Operational maturity
* Monitoring
* Testing
* Automation
* Cost

The goal is **not maximum availability**.

The goal is **appropriate availability**.

---

# The Availability-Cost Curve

```text
Availability
  ^
  |
100|                                *
   |                              *
99.999%                         *
   |                          *
99.99%                      *
   |                    *
99.9%                 *
   |              *
99%           *
   |      *
95%  *
   +-------------------------------------------->
          Infrastructure Cost & Complexity
```

Notice that moving from **99% to 99.9%** is relatively inexpensive.

Moving from **99.99% to 99.999%** often multiplies operational complexity and infrastructure cost.

---

# Understanding "Nines"

| Availability | Downtime / Year |
| ------------ | --------------- |
| 99%          | ~3.65 days      |
| 99.5%        | ~1.83 days      |
| 99.9%        | ~8.76 hours     |
| 99.95%       | ~4.38 hours     |
| 99.99%       | ~52.6 minutes   |
| 99.999%      | ~5.26 minutes   |

Moving from four nines to five nines usually requires architectural changes rather than simply adding more servers.

---

# 16.1 Redundancy

## Benefits

* Removes single points of failure.
* Faster recovery.
* Supports maintenance without downtime.
* Better resilience.

---

## Trade-offs

* Increased infrastructure cost.
* Data synchronization complexity.
* Higher operational overhead.
* More monitoring requirements.

---

## Business Decision

Use redundancy when downtime costs more than redundant infrastructure.

---

# 16.2 Active-Active vs Active-Passive

| Characteristic             | Active-Active | Active-Passive     |
| -------------------------- | ------------- | ------------------ |
| Infrastructure Cost        | High          | Medium             |
| Resource Utilization       | Excellent     | Lower              |
| Failover Time              | Near Zero     | Seconds to Minutes |
| Synchronization Complexity | High          | Medium             |
| Operational Complexity     | High          | Moderate           |
| Disaster Recovery          | Excellent     | Good               |

### Active-Active

Advantages

* No idle infrastructure
* Lowest downtime
* Better scalability

Disadvantages

* Conflict resolution
* Replication challenges
* Higher operational maturity required

---

### Active-Passive

Advantages

* Simpler architecture
* Easier recovery
* Lower cost

Disadvantages

* Idle resources
* Longer failover
* Capacity planning challenges

---

# 16.3 Multi-AZ vs Multi-Region

| Attribute           | Multi-AZ | Multi-Region |
| ------------------- | -------- | ------------ |
| Availability        | High     | Very High    |
| Disaster Recovery   | Limited  | Excellent    |
| Latency             | Low      | Higher       |
| Infrastructure Cost | Moderate | High         |
| Data Replication    | Easier   | More Complex |
| Regulatory Support  | Good     | Excellent    |

---

## When Multi-AZ Is Enough

Suitable for:

* Internal enterprise systems
* Regional businesses
* Most SaaS startups
* Departmental applications

---

## When Multi-Region Is Necessary

Required for:

* Global SaaS
* Financial institutions
* Government systems
* Mission-critical healthcare
* Global e-commerce

---

# 16.4 Synchronous vs Asynchronous Replication

## Synchronous

Advantages

* No data loss after acknowledgement.
* Strong consistency.
* Immediate replication.

Disadvantages

* Higher latency.
* Lower throughput.
* Slower writes.
* Sensitive to network latency.

---

## Asynchronous

Advantages

* Better performance.
* Faster writes.
* Lower latency.
* Better scalability.

Disadvantages

* Replication lag.
* Possible data loss during failover.
* Eventual consistency.

---

# 16.5 Cache

Benefits

* Faster response time.
* Lower database load.
* Better scalability.
* Higher throughput.

Trade-offs

* Cache invalidation complexity.
* Stale data.
* Memory cost.
* Operational complexity.

One of the classic computer science challenges remains:

> **There are only two hard things in Computer Science: cache invalidation and naming things.**

---

# 16.6 Retry

Retries increase resilience against temporary failures.

However:

Too many retries can collapse an already struggling dependency.

Good retry strategy includes:

* Maximum retry count
* Exponential backoff
* Random jitter
* Retry budget

---

# 16.7 Circuit Breaker

Advantages

* Protects dependencies.
* Prevents cascading failures.
* Faster recovery.
* Better customer experience.

Trade-offs

* Configuration complexity.
* Threshold tuning.
* Fallback implementation effort.

---

# 16.8 Message Queue

Advantages

* Loose coupling.
* Better resilience.
* Traffic buffering.
* Independent scaling.

Trade-offs

* Eventual consistency.
* Ordering complexity.
* Monitoring complexity.
* Duplicate message handling.

---

# 16.9 Kubernetes

Benefits

* Self-healing
* Autoscaling
* Rolling updates
* Declarative deployment

Trade-offs

* Operational complexity
* Learning curve
* Networking complexity
* Debugging distributed issues

---

# 16.10 Microservices

Advantages

* Independent deployment
* Failure isolation
* Team autonomy
* Technology flexibility

Trade-offs

* Network latency
* Distributed transactions
* Operational overhead
* Observability complexity

---

# Summary Trade-off Matrix

| Decision        | Improves          | Sacrifices                 |
| --------------- | ----------------- | -------------------------- |
| Multi-AZ        | Availability      | Cost                       |
| Multi-Region    | Disaster Recovery | Complexity                 |
| Cache           | Performance       | Consistency                |
| Retry           | Resilience        | Risk of Retry Storm        |
| Circuit Breaker | Stability         | Implementation Complexity  |
| Message Queue   | Fault Isolation   | Eventual Consistency       |
| Replication     | Availability      | Synchronization Complexity |
| Autoscaling     | Elasticity        | Infrastructure Cost        |
| Microservices   | Team Scalability  | Operational Complexity     |

---

# Architect's Perspective

An experienced architect evaluates every decision using four dimensions:

```text
Business Value

↓

Customer Experience

↓

Operational Simplicity

↓

Engineering Complexity
```

The best architecture is the one that provides the highest business value with acceptable complexity—not necessarily the most technically sophisticated solution.

---

# 17. Measurements

> **"You cannot improve what you cannot measure." — Peter Drucker**

High Availability is meaningless without measurable objectives.

Architects define measurable targets before implementation and continuously monitor them in production.

---

# Measurement Hierarchy

```text
Business Objective

↓

Service Level Agreement (SLA)

↓

Service Level Objective (SLO)

↓

Service Level Indicator (SLI)

↓

Metrics

↓

Dashboards

↓

Alerts
```

---

# 17.1 Service Level Indicator (SLI)

An SLI is a quantitative measure of service behavior.

Examples include:

* Successful request percentage
* Request latency
* Error rate
* Availability percentage
* Throughput

Examples:

| SLI          | Example      |
| ------------ | ------------ |
| Availability | 99.95%       |
| Latency      | P95 < 250 ms |
| Error Rate   | < 0.1%       |
| Success Rate | 99.9%        |
| Throughput   | 15,000 RPS   |

---

# 17.2 Service Level Objective (SLO)

An SLO defines the target value for an SLI.

Examples:

| SLI           | SLO          |
| ------------- | ------------ |
| Availability  | 99.95%       |
| Latency       | P95 < 300 ms |
| Error Rate    | <0.05%       |
| Recovery Time | <15 minutes  |

---

# 17.3 Service Level Agreement (SLA)

An SLA is a contractual commitment made to customers.

Failure to meet an SLA may result in:

* Financial penalties
* Service credits
* Contract termination
* Reputation loss

Example:

```text
SLA

99.99%

Annual Availability
```

---

# 17.4 Availability Formula

The standard formula is:

```text
Availability =

Uptime

-------------------------

Uptime + Downtime
```

Example:

```text
365 Days

Downtime = 4 Hours

Availability

=

8756

/

8760

=

99.95%
```

---

# 17.5 MTTR

Mean Time To Recovery

Measures:

Average time required to restore service.

Formula:

```text
Total Recovery Time

-------------------------

Number of Incidents
```

Lower MTTR indicates faster recovery and generally better availability.

---

# 17.6 MTBF

Mean Time Between Failures

Formula:

```text
Operational Time

------------------------

Number of Failures
```

Higher MTBF indicates more reliable systems.

---

# 17.7 Recovery Time Objective (RTO)

Maximum acceptable outage duration.

Example:

| System      | RTO        |
| ----------- | ---------- |
| Banking     | <5 minutes |
| Internal HR | 2 hours    |
| Analytics   | 24 hours   |

---

# 17.8 Recovery Point Objective (RPO)

Maximum acceptable data loss.

Example:

| Application | RPO       |
| ----------- | --------- |
| Banking     | 0 seconds |
| Retail      | 5 minutes |
| Analytics   | 1 hour    |

---

# 17.9 Latency Percentiles

Average latency hides user experience.

Instead, architects monitor:

* P50
* P90
* P95
* P99
* P99.9

Example:

| Percentile | Response Time |
| ---------- | ------------- |
| P50        | 80 ms         |
| P95        | 210 ms        |
| P99        | 650 ms        |
| P99.9      | 2.4 sec       |

Tail latency often determines perceived system performance.

---

# 17.10 Throughput

Measures completed work.

Examples:

* Requests Per Second (RPS)
* Transactions Per Second (TPS)
* Messages Per Second
* Orders Per Minute

---

# 17.11 Infrastructure Metrics

Common metrics include:

## CPU

Monitor:

* Average utilization
* Peak utilization
* Saturation
* Load average

---

## Memory

Track:

* Heap usage
* Garbage collection
* Page faults
* Swap usage

---

## Disk

Monitor:

* Disk utilization
* IOPS
* Read latency
* Write latency
* Available storage

---

## Network

Monitor:

* Bandwidth
* Packet loss
* TCP errors
* Latency
* Connection count

---

## Queue Metrics

Examples:

* Queue depth
* Consumer lag
* Message age
* Retry count
* Dead-letter queue size

---

## Cache Metrics

Monitor:

* Cache hit ratio
* Cache miss ratio
* Evictions
* Memory usage
* Expiration rate

A declining cache hit ratio may indicate inefficient caching or changing access patterns.

---

## Database Metrics

Track:

* Active connections
* Slow queries
* Lock contention
* Replication lag
* Transaction throughput

---

# Error Budget

An SLO implicitly defines an **error budget**—the amount of unreliability a service can tolerate while still meeting its objective.

For example:

* SLO: **99.9% availability**
* Error budget: **0.1% downtime**

Error budgets help balance feature delivery with operational stability. If the budget is exhausted, engineering effort should shift from new features to improving reliability and availability.

---

## Measurement Dashboard

A production dashboard for High Availability typically includes:

| Category       | Example Metrics                          |
| -------------- | ---------------------------------------- |
| Availability   | Uptime %, Error Rate                     |
| Performance    | P95/P99 Latency, Throughput              |
| Infrastructure | CPU, Memory, Disk, Network               |
| Database       | Replication Lag, Slow Queries            |
| Cache          | Hit Ratio, Evictions                     |
| Messaging      | Queue Depth, Consumer Lag                |
| Recovery       | MTTR, Active Incidents                   |
| Business       | Order Success Rate, Payment Success Rate |

These measurements provide a comprehensive view of system health from both technical and business perspectives.

---

**End of Part 6.**

The next part will cover **Section 18: Operational Considerations**, including monitoring, logging, tracing, alerting, capacity planning, scaling strategies, backup and restore, disaster recovery, chaos engineering, maintenance, and cost monitoring, followed by **Production Incidents** and **Anti-patterns**.
