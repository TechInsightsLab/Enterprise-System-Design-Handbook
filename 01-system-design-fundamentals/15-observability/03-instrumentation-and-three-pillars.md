
# 3. Instrumentation

> **Instrumentation is the process of adding code or runtime mechanisms that generate telemetry such as metrics, logs, traces, and events from an application.**

Applications do not become observable automatically.

They must be instrumented.

Instrumentation enables applications to describe:

- What they are doing
- How long operations take
- What failed
- Which dependencies are involved
- Which users are affected

Without instrumentation,

there is no observability.

---

# Why Instrumentation?

Consider an API.

```text
POST /orders
```

Without instrumentation.

The request is processed.

Nothing is recorded.

If the API becomes slow,

nobody knows why.

---

With instrumentation.

```text
POST /orders

↓

Request Started

↓

Database Query

↓

Kafka Publish

↓

Payment API

↓

Request Completed

↓

Latency Recorded

↓

Trace Generated
```

Every important operation becomes visible.

---

# Instrumentation Goals

Instrumentation should answer:

- What happened?
- When did it happen?
- How long did it take?
- Which component executed?
- Which dependency was called?
- Did it succeed?
- Why did it fail?

---

# Instrumentation Types

Modern systems typically generate four telemetry signals.

```text
Instrumentation

├── Metrics
├── Logs
├── Traces
└── Events
```

Each serves a different purpose.

---

# Automatic vs Manual Instrumentation

There are two primary approaches.

---

## Automatic Instrumentation

Telemetry is generated.

Without modifying application code.

Example.

```text
Application

↓

Java Agent

↓

OpenTelemetry

↓

Telemetry
```

Advantages.

- Fast adoption
- Minimal code changes
- Consistent instrumentation

Limitations.

- Limited business context
- Less control
- Framework dependent

---

## Manual Instrumentation

Developers explicitly add instrumentation.

Example.

```java
logger.info("Order Created");

counter.increment();

span.addEvent("Payment Started");
```

Advantages.

- Rich business context
- Precise measurements
- Custom telemetry

Limitations.

- More development effort
- Requires discipline

---

# Enterprise Recommendation

Use both.

```text
Automatic

+

Manual

↓

Complete Observability
```

Automatic instrumentation captures technical telemetry.

Manual instrumentation captures business telemetry.

---

# Where Should We Instrument?

Instrument important business operations.

Examples.

```text
Login

Order Creation

Payment

Inventory Check

Notification

Search

Checkout
```

Avoid instrumenting every line of code.

Focus on meaningful operations.

---

# Example

E-Commerce Checkout.

```text
Customer

↓

Checkout

↓

Inventory

↓

Payment

↓

Shipping

↓

Notification
```

Each step should generate telemetry.

---

# Instrumenting External Calls

Every call to an external dependency should be instrumented.

Examples.

```text
Database

Redis

Kafka

REST API

gRPC

Object Storage
```

External dependencies are common sources of latency.

---

# Instrumenting Business Events

Technical metrics alone are insufficient.

Capture business events.

Examples.

```text
Order Created

Payment Failed

Cart Abandoned

Coupon Applied

Subscription Renewed
```

These become valuable business metrics.

---

# Avoid Over-Instrumentation

Too much telemetry creates problems.

Examples.

- Higher storage costs
- Slower searches
- Alert fatigue
- Increased network traffic

Instrument.

Important.

Not everything.

---

# Characteristics of Good Instrumentation

Good instrumentation is:

- Consistent
- Lightweight
- Structured
- Correlated
- Meaningful
- Low overhead

---

# Naming Conventions

Telemetry names should be predictable.

Example.

```text
order.created

payment.completed

inventory.checked
```

Instead of.

```text
abc

event1

xyz
```

Consistency improves analysis.

---

# Correlation

Every telemetry signal should be correlated.

Example.

```text
Request ID

↓

Metric

↓

Log

↓

Trace

↓

Dashboard
```

Correlation enables end-to-end investigation.

---

# Instrumentation Architecture

```text
Application

↓

Instrumentation Library

↓

OpenTelemetry SDK

↓

Collector

↓

Observability Platform
```

Instrumentation should remain independent of the backend vendor wherever practical.

---

# Business vs Technical Instrumentation

## Technical

Examples.

- CPU
- Memory
- HTTP Latency
- Database Calls

---

## Business

Examples.

- Orders Per Minute
- Revenue
- Failed Payments
- Premium User Logins

Architects should encourage both.

---

# Common Mistakes

### Logging Instead of Instrumenting

Writing log messages.

Is not enough.

Generate metrics.

And traces.

Too.

---

### Instrumenting Every Function

Creates excessive telemetry.

Instrument meaningful operations.

---

### Ignoring External Dependencies

Many production issues originate outside the application.

Instrument dependency calls.

---

### No Correlation IDs

Without correlation.

Telemetry cannot be connected.

---

# Architect's Perspective

Instrumentation is an architectural decision.

Not merely a development task.

Well-instrumented systems become easier to:

- Operate
- Scale
- Debug
- Improve

Poorly instrumented systems become operational black boxes.

---

# 4. The Three Pillars of Observability

Historically.

Observability has been built upon three primary telemetry signals.

```text
Metrics

↓

Logs

↓

Traces
```

Together.

They provide a comprehensive understanding.

Of system behavior.

Although modern observability increasingly includes events, profiles, and continuous profiling, these three pillars remain the foundation.

---

# Pillar 1 — Metrics

> **Metrics are numerical measurements collected over time that describe the health and performance of a system.**

Examples.

```text
CPU

Memory

Latency

Request Count

Error Rate

Cache Hit Ratio
```

Metrics answer.

```
What is happening?
```

---

# Characteristics

Metrics are:

- Numeric
- Time-series
- Lightweight
- Aggregated
- Efficient

---

# Example

```text
Requests/sec

↓

1,250

----------------

Latency

↓

125 ms

----------------

CPU

↓

68%
```

Metrics quickly reveal trends.

---

# Strengths

Metrics are excellent for:

- Dashboards
- Alerts
- Capacity planning
- Trend analysis
- SLA monitoring

---

# Limitations

Metrics cannot explain.

Why.

Something happened.

They indicate symptoms.

Not causes.

---

# Pillar 2 — Logs

> **Logs are timestamped records describing discrete events that occurred within a system.**

Logs answer.

```
What exactly happened?
```

---

# Example

```text
2026-07-09 10:15:22

Order Created

OrderId=5001

Customer=123
```

---

# Characteristics

Logs are:

- Detailed
- Event-based
- Textual or structured
- Searchable

---

# Logs Help Answer

- Which user failed?
- Which order failed?
- Which exception occurred?
- Which deployment introduced errors?

---

# Strengths

Logs provide:

- Context
- Error details
- Business events
- Audit history

---

# Limitations

Logs are difficult to aggregate.

Large systems may generate terabytes of logs daily.

Without structure and indexing,

analysis becomes difficult.

---

# Pillar 3 — Traces

> **Traces describe the complete journey of a request across multiple distributed services.**

Tracing answers.

```
Where did the request spend its time?
```

---

# Example

```text
Customer

↓

Gateway

↓

Order Service

↓

Inventory

↓

Payment

↓

Database
```

Each step.

Recorded.

As part of one trace.

---

# Characteristics

Traces provide:

- End-to-end visibility
- Dependency mapping
- Latency breakdown
- Root cause identification

---

# Strengths

Excellent for:

- Microservices
- Distributed debugging
- Performance analysis
- Root cause analysis

---

# Limitations

Tracing requires:

- Context propagation
- Instrumentation
- Storage
- Sampling strategies

---

# Comparing the Three Pillars

| Aspect | Metrics | Logs | Traces |
|---------|---------|------|--------|
| Data Type | Numeric | Events | Request Flow |
| Primary Question | What? | What happened? | Why and where? |
| Storage | Low | Medium to High | Medium |
| Alerts | Excellent | Limited | Limited |
| Root Cause | Weak | Good | Excellent |
| Business Context | Moderate | High | High |

---

# How They Work Together

Consider a checkout failure.

### Metrics

Reveal.

```
Latency Increased
```

---

### Logs

Reveal.

```
Payment Timeout
```

---

### Trace

Shows.

```text
Checkout

↓

Payment Service

↓

Third-Party API

↓

8 Second Delay
```

Together.

They explain.

The entire incident.

---

# Three Pillars Workflow

```text
Metrics

↓

Alert

↓

Logs

↓

Error Details

↓

Trace

↓

Root Cause

↓

Fix
```

This workflow is common in production operations.

---

# Enterprise Example

Online Shopping.

Customer reports.

```
Payment Failed
```

Metrics.

```
Error Rate

↑
```

Logs.

```
Payment Gateway Timeout
```

Trace.

```text
Order

↓

Payment

↓

External Provider

↓

Timeout
```

Root cause identified.

Within minutes.

---

# Beyond the Three Pillars

Modern observability platforms increasingly incorporate additional telemetry.

Examples.

```text
Metrics

Logs

Traces

Events

Continuous Profiling

Real User Monitoring (RUM)

Synthetic Monitoring
```

Many architects now refer to these collectively as **observability signals** rather than limiting the discussion to three pillars.

---

# Architect's Perspective

The three pillars complement each other.

Metrics tell you **something is wrong**.

Logs tell you **what happened**.

Traces tell you **where and why it happened**.

No single pillar is sufficient for operating modern distributed systems.

Architects should design systems that generate all three consistently, correlate them through shared context, and use them together to minimize investigation time during production incidents.

---

**End of Part 2B**

The next part will cover:

- **Metrics Deep Dive**
- **Counters**
- **Gauges**
- **Histograms**
- **Summaries**
- **RED Method**
- **USE Method**
- **Four Golden Signals**
- **Business Metrics**
- **Custom Metrics**
