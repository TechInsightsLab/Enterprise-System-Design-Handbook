
# 38. Service Level Indicators (SLI)

> **A Service Level Indicator (SLI) is a quantitative measurement that reflects the actual performance or reliability of a service from the user's perspective.**

An SLI answers the question:

> **"How well is the service actually performing?"**

It is a measured value.

Not a target.

---

# Why SLI Matters

Imagine.

An online payment platform.

Customers ask.

```
Is the payment service reliable?
```

Instead of answering.

```
Looks healthy.
```

We measure.

```text
99.98%

Successful Payments
```

This measurable value.

Is an SLI.

---

# Examples of SLIs

```text
Availability

↓

99.95%

--------------------

Latency

↓

P95 = 180 ms

--------------------

Error Rate

↓

0.2%

--------------------

Payment Success

↓

99.9%
```

SLIs describe reality.

---

# Common SLI Categories

Enterprise systems usually measure.

```text
Availability

Latency

Error Rate

Throughput

Durability

Correctness

Freshness
```

Each represents a different aspect of service quality.

---

# Availability SLI

Measures.

Whether users can access the service.

Example.

```text
Successful Requests

999,500

Total Requests

1,000,000

Availability

99.95%
```

---

# Latency SLI

Measures.

Response time.

Example.

```text
P95

200 ms
```

Most users.

Receive responses.

Within 200 milliseconds.

---

# Error Rate SLI

Measures.

Percentage of failed requests.

Example.

```text
Total Requests

1,000,000

Failures

2,000

↓

Error Rate

0.2%
```

---

# Throughput SLI

Measures.

Amount of useful work completed.

Examples.

```text
Requests/sec

Transactions/sec

Orders/minute

Messages/sec
```

---

# Business SLIs

Not every SLI is technical.

Business SLIs include.

```text
Payment Success

Checkout Completion

Search Success

Order Completion

Login Success
```

Business SLIs often matter more than infrastructure metrics.

---

# Characteristics of Good SLIs

Good SLIs are:

- Customer-focused
- Measurable
- Objective
- Continuously collected
- Representative of user experience

---

# Poor SLI Examples

```text
CPU Usage

Memory Usage

Disk Usage
```

These are infrastructure metrics.

Customers do not care.

About CPU.

They care.

Whether the application works.

---

# Good SLI Examples

```text
Checkout Success

Payment Latency

Login Availability

Search Response Time
```

These reflect customer experience.

---

# SLI Architecture

```text
Customer

↓

Application

↓

Telemetry

↓

SLI Calculation

↓

Dashboard
```

SLIs are calculated continuously.

---

# 39. Service Level Objectives (SLO)

> **A Service Level Objective (SLO) is the target value or reliability goal for one or more SLIs.**

SLI measures.

Reality.

SLO defines.

The goal.

---

# Example

SLI.

```
Availability

99.94%
```

SLO.

```
Availability

99.90%
```

Service.

Meeting objective.

---

# Difference

```text
SLI

↓

Current Performance

---------------------

SLO

↓

Desired Performance
```

---

# Examples

```text
Availability

99.95%

--------------------

Latency

P95 < 250 ms

--------------------

Error Rate

< 0.5%

--------------------

Payment Success

> 99.9%
```

These are SLOs.

---

# Why SLOs Matter

Without SLOs.

Teams argue.

```
System is slow.
```

Versus.

```
Looks fine.
```

SLOs provide.

Objective expectations.

---

# Example

Checkout API.

SLO.

```
99%

Requests

< 300 ms
```

Measured SLI.

```
99.4%

< 300 ms
```

Objective achieved.

---

# Choosing SLOs

Good SLOs should be:

- Realistic
- Customer focused
- Measurable
- Business aligned

Avoid impossible objectives.

Such as.

```
100% Availability
```

---

# Multiple SLOs

A single service.

Often has.

Multiple objectives.

Example.

```text
Availability

99.95%

-------------------

P95 Latency

< 200 ms

-------------------

Error Rate

< 0.2%
```

---

# Enterprise Example

Payment Service.

```text
Availability

99.99%

↓

Latency

P95 < 300 ms

↓

Payment Success

99.95%
```

Business expectations.

Clearly defined.

---

# 40. Service Level Agreements (SLA)

> **A Service Level Agreement (SLA) is a contractual commitment between a service provider and a customer that defines expected service levels and the consequences of failing to meet them.**

Unlike SLOs.

SLAs.

Are legal.

Or commercial agreements.

---

# Relationship

```text
SLI

↓

Measures

----------------

SLO

↓

Target

----------------

SLA

↓

Contract
```

---

# Example

Cloud Provider.

Promises.

```
99.9%

Monthly Availability
```

If violated.

Customer receives.

Service credits.

---

# SLA Components

Typical SLA includes.

- Availability
- Response Time
- Support Hours
- Recovery Time
- Compensation
- Exclusions

---

# Example

```text
Availability

99.95%

--------------------

Support

24×7

--------------------

P1 Response

15 Minutes
```

Contractually agreed.

---

# Internal vs External

Internal teams.

Usually define.

SLOs.

Customers.

Usually receive.

SLAs.

---

# Example

Engineering Target.

```
99.99%
```

Customer SLA.

```
99.9%
```

Engineering keeps buffer.

Above contractual commitments.

---

# 41. Error Budget

> **An Error Budget is the amount of acceptable failure permitted while still meeting the SLO.**

Error budgets balance.

Innovation.

And.

Reliability.

---

# Example

SLO.

```
99.9%
```

Allowed downtime.

```
0.1%
```

This.

Is the error budget.

---

# Monthly Example

30 Days.

Availability.

99.9%.

Allowed downtime.

Approximately.

43 Minutes.

Teams may "spend" this budget through outages or controlled risk.

---

# Why Error Budgets?

Without error budgets.

Teams push.

Maximum reliability.

Slowing innovation.

Or.

Maximum features.

Reducing stability.

Error budgets create balance.

---

# Error Budget Philosophy

If budget remains.

Development can proceed.

Normally.

If budget exhausted.

Focus shifts.

To reliability improvements.

---

# Example

Payment Platform.

Error Budget.

```
43 Minutes
```

Unexpected outage.

```
35 Minutes
```

Remaining budget.

```
8 Minutes
```

High-risk deployments.

May be postponed.

---

# Benefits

Error budgets help:

- Prioritize reliability
- Balance feature delivery
- Reduce production risk
- Enable objective decision making

---

# 42. Burn Rate

> **Burn Rate measures how quickly an error budget is being consumed.**

It predicts.

Whether the SLO.

Will be violated.

Before the reporting period ends.

---

# Example

Monthly error budget.

```
43 Minutes
```

Consumed.

```
20 Minutes

Today
```

Burn rate.

Extremely high.

Immediate investigation required.

---

# Burn Rate Formula

Conceptually.

```text
Actual Error Rate

÷

Allowed Error Rate

=

Burn Rate
```

---

# Example

Burn Rate.

```
1×

Normal Consumption
```

Healthy.

---

Burn Rate.

```
10×

Normal
```

Budget.

Will disappear.

Rapidly.

---

# Why Burn Rate Alerts?

Traditional alerts.

Trigger.

After.

SLO violation.

Burn rate alerts.

Warn.

Before.

Violation occurs.

---

# Example

Application.

Still meeting.

99.9%.

But.

Burn rate.

Increasing rapidly.

Alert generated.

Engineers intervene.

Before customers experience widespread impact.

---

# Multi-Window Burn Rate

Many SRE teams monitor multiple windows.

Example.

```text
5 Minutes

↓

1 Hour

↓

6 Hours
```

This detects.

Both.

Sudden incidents.

And.

Slow degradation.

---

# Reliability Dashboard

Example.

```text
Availability

99.96%

--------------------

Latency

180 ms

--------------------

Error Budget

62% Remaining

--------------------

Burn Rate

0.8×

Healthy
```

Operations teams.

Can immediately assess reliability.

---

# SLI vs SLO vs SLA

| SLI | SLO | SLA |
|------|------|------|
| Measurement | Target | Contract |
| Operational | Engineering Goal | Business Commitment |
| Continuous | Internal Objective | Customer Agreement |

---

# SRE Perspective

Site Reliability Engineering (SRE) emphasizes.

```
Measure

↓

Objective

↓

Budget

↓

Improve
```

Rather than.

```
Guess

↓

Hope

↓

React
```

Reliability becomes measurable.

---

# Common Mistakes

### Monitoring Infrastructure Instead of User Experience

Customers care about:

- Successful logins
- Successful payments
- Fast checkout

Not CPU utilization.

---

### Setting Unrealistic SLOs

```
100%

Availability
```

Usually impractical.

Choose achievable objectives.

---

### No Error Budget

Without an error budget.

Engineering lacks an objective framework for balancing reliability and feature velocity.

---

### Confusing SLA with SLO

SLO.

Internal engineering target.

SLA.

Customer commitment.

Do not confuse them.

---

### Ignoring Burn Rate

Waiting until the SLO is violated.

Reduces recovery time.

Monitor burn rate continuously.

---

# Architect's Perspective

SLIs, SLOs, SLAs, and Error Budgets transform reliability from a subjective opinion into an engineering discipline.

Rather than asking:

> "Is the system reliable?"

Architects can answer:

- Availability: **99.97%**
- P95 Latency: **165 ms**
- Error Rate: **0.08%**
- Error Budget Remaining: **74%**
- Burn Rate: **0.6×**

These measurements allow engineering teams to make informed decisions about deployments, scaling, and operational risk.

A mature architecture is not judged by how reliable it *claims* to be.

It is judged by how reliably it **measures, manages, and continuously improves** customer experience.

---

**End of Part 6**

The next part will cover:

- **Cloud-Native Observability**
- **Kubernetes Observability**
- **Container Monitoring**
- **Service Mesh Observability**
- **Cloud Monitoring Services**
- **Multi-Region Observability**
- **Cost Optimization for Observability**
