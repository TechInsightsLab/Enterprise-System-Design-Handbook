
# 10. RED Method

> **The RED Method is an observability framework for monitoring services by focusing on Request Rate, Error Rate, and Duration (Latency).**

The RED Method was introduced by Tom Wilkie and is widely used for monitoring:

- Microservices
- REST APIs
- gRPC Services
- Event Processing Systems

Instead of collecting hundreds of metrics,

RED focuses on the three metrics that matter most for service health.

---

# Why RED?

Imagine an Order Service.

Management asks:

```
Is the service healthy?
```

Instead of checking:

- CPU
- Memory
- Disk
- Threads
- Network

RED asks only:

```text
Request Rate

↓

Error Rate

↓

Duration
```

These three metrics quickly reveal service health.

---

# RED Architecture

```text
Client

↓

Order Service

↓

Metrics

├── Request Rate
├── Error Rate
└── Duration

↓

Dashboard
```

---

# R — Request Rate

> **Request Rate measures how many requests the service is handling per unit of time.**

Usually measured as:

```text
Requests / Second (RPS)

Requests / Minute
```

---

# Example

```text
09:00

500 RPS

↓

09:05

520 RPS

↓

09:10

510 RPS
```

Traffic remains stable.

---

# Why Request Rate Matters

Request rate helps answer:

- Is traffic increasing?
- Is traffic decreasing?
- Is autoscaling required?
- Did a deployment reduce traffic?
- Is a DDoS attack occurring?

---

# Example

Normal traffic.

```text
600 RPS
```

Suddenly.

```text
15,000 RPS
```

Possible.

- Marketing campaign
- Bot attack
- DDoS attack

Requires investigation.

---

# E — Error Rate

> **Error Rate measures the percentage or number of failed requests.**

Example.

```text
Requests

1000

↓

Failures

20

↓

Error Rate

2%
```

---

# What Counts as an Error?

Depends on the application.

Examples.

- HTTP 5xx
- Exceptions
- Timeouts
- Failed Payments
- Database Errors

Some systems also monitor selected 4xx responses depending on business requirements.

---

# Example

```text
HTTP 200

980

↓

HTTP 500

20
```

Error Rate.

```
2%
```

---

# Why Error Rate Matters

A service may appear healthy.

CPU.

Normal.

Memory.

Normal.

But.

```text
40%

Requests

Fail
```

Customers experience outages.

---

# D — Duration (Latency)

> **Duration measures how long it takes to process a request from start to finish.**

Measured using:

- Average
- P50
- P90
- P95
- P99

Percentiles are preferred over averages.

---

# Example

```text
Average

100 ms

P95

320 ms

P99

900 ms
```

Most users.

Receive fast responses.

Some users.

Experience delays.

---

# Why Duration Matters

Latency directly affects:

- User experience
- Revenue
- Conversion
- Customer satisfaction

Even successful requests.

Can represent failures.

If they are excessively slow.

---

# RED Dashboard

```text
Order Service

──────────────────────

Request Rate

620 RPS

----------------------

Error Rate

0.4%

----------------------

P95 Latency

185 ms
```

A quick glance.

Shows service health.

---

# Enterprise Example

Payment Service.

```text
Requests

900/sec

↓

Errors

0.3%

↓

Latency

120 ms
```

Healthy.

---

Suddenly.

```text
Requests

900/sec

↓

Errors

18%

↓

Latency

2.5 Seconds
```

Major production incident.

---

# RED Advantages

- Simple
- Easy to implement
- Excellent for APIs
- Great for dashboards
- Easy alerting
- Technology independent

---

# RED Limitations

RED focuses.

On services.

Not infrastructure.

Need additional metrics.

For:

- CPU
- Memory
- Disk
- Network

---

# When to Use RED

Ideal for:

- REST APIs
- GraphQL
- gRPC
- Microservices
- Serverless APIs
- Internal Services

---

# 11. USE Method

> **The USE Method monitors resources by measuring Utilization, Saturation, and Errors.**

The USE Method was created by Brendan Gregg.

Unlike RED,

USE focuses on infrastructure resources.

---

# USE Architecture

```text
Infrastructure

↓

CPU

Memory

Disk

Network

↓

USE Metrics
```

---

# U — Utilization

> **Utilization measures how busy a resource is.**

Examples.

```text
CPU

65%

------------------

Disk

72%

------------------

Network

40%
```

---

# Questions Answered

- Is CPU fully utilized?
- Is disk busy?
- Is memory exhausted?
- Is network bandwidth sufficient?

---

# S — Saturation

> **Saturation measures the amount of work waiting for a resource.**

Examples.

- CPU Run Queue
- Thread Queue
- Disk Queue
- Kafka Consumer Lag
- Database Connection Queue

---

# Example

Restaurant analogy.

Tables.

Occupied.

Customers waiting.

Queue growing.

Restaurant.

Saturated.

---

Similarly.

CPU.

100% busy.

New work waits.

System slows.

---

# E — Errors

Errors represent.

Resource failures.

Examples.

```text
Disk Errors

Packet Loss

OOM Errors

Filesystem Errors

Network Retransmissions
```

Errors indicate hardware or infrastructure problems.

---

# USE Dashboard

```text
CPU

Utilization

72%

-------------------

CPU Queue

5

-------------------

CPU Errors

0
```

Repeat.

For.

Memory.

Disk.

Network.

---

# Enterprise Example

Database Server.

```text
CPU

95%

↓

Connection Queue

150

↓

Errors

Timeouts
```

Resource saturated.

Scaling required.

---

# RED vs USE

| RED | USE |
|------|-----|
| Services | Infrastructure |
| Request Rate | Utilization |
| Error Rate | Saturation |
| Duration | Errors |

Both complement each other.

---

# When to Use USE

Best for:

- Kubernetes Nodes
- Virtual Machines
- Databases
- Kafka Brokers
- Redis
- Load Balancers
- Storage Systems

---

# 12. Google's Four Golden Signals

Google Site Reliability Engineering (SRE) introduced four key metrics.

```text
Latency

↓

Traffic

↓

Errors

↓

Saturation
```

These signals provide a balanced operational view.

---

# 1. Latency

Measures.

Request duration.

Example.

```
P95 = 220 ms
```

Customers experience latency.

Not averages.

---

# 2. Traffic

Traffic represents.

Demand.

Examples.

- Requests/sec
- Transactions/sec
- Orders/minute
- Messages/sec

Traffic helps estimate system load.

---

# 3. Errors

Errors measure.

Failed work.

Examples.

- HTTP 500
- Payment Failure
- Database Timeout
- Authentication Failure

---

# 4. Saturation

Measures.

How close the system is.

To capacity.

Examples.

- CPU Usage
- Memory Usage
- Queue Length
- Thread Pool Usage
- Kafka Lag

High saturation indicates future failures.

---

# Relationship

```text
Traffic

↓

Latency

↓

Errors

↓

Saturation
```

As traffic increases.

Saturation rises.

Latency increases.

Errors eventually appear.

---

# Example

Normal.

```text
Traffic

500 RPS

Latency

80 ms

Errors

0%

CPU

45%
```

Peak Sale.

```text
Traffic

2500 RPS

Latency

700 ms

Errors

12%

CPU

98%
```

Golden Signals clearly reveal.

Capacity issues.

---

# RED vs Golden Signals

| RED | Golden Signals |
|------|----------------|
| Request Rate | Traffic |
| Error Rate | Errors |
| Duration | Latency |
| — | Saturation |

Google's model extends RED.

By adding infrastructure capacity awareness.

---

# 13. Business Metrics

Technical metrics explain.

System health.

Business metrics explain.

Business success.

---

# Examples

```text
Orders Per Minute

Revenue Per Hour

Cart Abandonment

Payment Success Rate

Login Success Rate

Average Order Value

New Users

Returning Users
```

Executives monitor these.

Every day.

---

# Example Dashboard

```text
Orders

2,500/hour

------------------

Revenue

₹12,40,000

------------------

Checkout Success

98.9%

------------------

Failed Payments

0.6%
```

These metrics connect engineering to business outcomes.

---

# Technical vs Business Metrics

| Technical | Business |
|------------|----------|
| CPU | Revenue |
| Memory | Orders |
| Latency | Conversion Rate |
| Error Rate | Payment Success |
| Disk | Customer Signups |

Architects need both.

---

# 14. Service-Level Metrics

Every service should expose key operational metrics.

Example.

Order Service.

```text
Request Rate

Latency

Error Rate

Thread Pool Usage

Database Latency

Cache Hit Ratio

Kafka Publish Time
```

Consistent metrics simplify operations.

---

# 15. Custom Metrics

Applications should expose metrics.

Specific to the business domain.

Example.

Ride Sharing.

```text
Trips Started

Trips Completed

Drivers Online

Ride Acceptance Rate
```

---

Banking.

```text
Transfers

Loan Approvals

Fraud Detection Rate

Payment Success
```

---

Healthcare.

```text
Appointments

Prescriptions

Patient Admissions

Lab Results
```

Custom metrics are often more valuable than infrastructure metrics.

---

# Dashboard Design Principles

A good dashboard should answer.

Within 30 seconds.

- Is the service healthy?
- What changed?
- Where is the bottleneck?
- Who is affected?
- Should I investigate?

Avoid dashboards containing hundreds of charts.

---

# Common Mistakes

### Alerting on Every Metric

Not every metric requires an alert.

Alert only on actionable conditions.

---

### Using Average Latency

Averages hide slow users.

Prefer percentiles.

---

### Ignoring Saturation

Healthy latency today.

May become tomorrow's outage.

Monitor resource saturation.

---

### No Business Metrics

A healthy infrastructure.

Does not guarantee.

A healthy business.

---

### Too Many Dashboards

Different teams create different dashboards.

Without standards.

Standardize enterprise dashboards.

---

# Architect's Perspective

Frameworks like **RED**, **USE**, and the **Four Golden Signals** provide proven ways to monitor production systems without overwhelming engineers with thousands of metrics.

A mature observability strategy typically combines:

- **RED** for service health.
- **USE** for infrastructure health.
- **Golden Signals** for overall system reliability.
- **Business Metrics** for customer and business outcomes.

The most valuable dashboards are not those with the most graphs.

They are the ones that help engineers detect, diagnose, and resolve production issues quickly while showing how technical performance impacts the business.

---

**End of Part 3B**

The next part will cover:

- **Logging Deep Dive**
- **Structured Logging**
- **Log Levels**
- **Correlation IDs**
- **Trace IDs**
- **Log Aggregation**
- **Centralized Logging**
- **Log Retention**
- **Log Sampling**
- **Sensitive Data Masking**
