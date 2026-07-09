
# 16. Logging Deep Dive

> **Logs are timestamped records of events that occurred within a system during its execution.**

Logs provide the detailed narrative of what happened inside an application.

Unlike metrics.

Which answer:

> "How much?"

Logs answer:

> "Exactly what happened?"

---

# Why Logging Matters

Imagine a payment fails.

Metrics show.

```
Payment Errors

↑
```

But metrics cannot explain.

- Which customer?
- Which order?
- Which API?
- Which exception?
- Which database query?

Logs provide those answers.

---

# Example

```text
2026-07-09T10:15:30Z

Payment Failed

Order=5001

Customer=12345

Reason=Gateway Timeout

Duration=4.2 sec
```

One log entry.

Can immediately explain.

The failure.

---

# Logging Lifecycle

```text
Application

↓

Generate Log

↓

Log Collector

↓

Central Log Platform

↓

Search

↓

Dashboard

↓

Engineer
```

Logs should flow automatically.

From every service.

---

# Why Every Service Must Produce Logs

Modern applications contain.

```text
Gateway

↓

Order Service

↓

Inventory

↓

Payment

↓

Notification
```

Every service.

Should log.

Its important events.

Otherwise.

The request journey becomes incomplete.

---

# What Should Be Logged?

Examples.

```text
Application Start

Application Stop

HTTP Requests

Authentication

Authorization

Database Errors

Cache Misses

Kafka Publish

External API Calls

Business Events

Exceptions
```

---

# What Should NOT Be Logged?

Never log.

```text
Passwords

OTP

Credit Card Numbers

Private Keys

JWT Secrets

Database Passwords

API Secrets
```

Sensitive data.

Must never appear.

In logs.

---

# Characteristics of Good Logs

Good logs are:

- Structured
- Consistent
- Searchable
- Correlated
- Timestamped
- Actionable

Poor logs.

Increase MTTR.

---

# 17. Structured Logging

> **Structured Logging stores log entries in a machine-readable format such as JSON instead of plain text.**

Traditional logs.

Look like.

```text
User logged in successfully
```

Easy for humans.

Hard for machines.

---

# Structured Log

```json
{
  "timestamp":"2026-07-09T10:15:30Z",
  "service":"order-service",
  "level":"INFO",
  "orderId":"5001",
  "customerId":"123",
  "message":"Order Created"
}
```

Easy to:

- Search
- Filter
- Aggregate
- Analyze

---

# Plain Text vs Structured

| Plain Text | Structured |
|------------|------------|
| Human Friendly | Human + Machine Friendly |
| Hard Search | Easy Search |
| Difficult Aggregation | Easy Aggregation |
| Limited Filtering | Powerful Filtering |

---

# Enterprise Recommendation

Always prefer.

Structured logging.

For production systems.

---

# Log Schema

A typical enterprise log contains.

```text
Timestamp

Level

Service

Host

Environment

Request ID

Trace ID

User

Operation

Message

Exception

Duration
```

Consistency across services.

Is critical.

---

# Example

```json
{
 "service":"payment-service",
 "level":"ERROR",
 "traceId":"abc123",
 "requestId":"xyz789",
 "message":"Payment Gateway Timeout",
 "duration":4200
}
```

This log can be automatically indexed.

And correlated.

---

# 18. Log Levels

Log levels indicate.

The importance.

Of an event.

---

# Standard Levels

```text
TRACE

↓

DEBUG

↓

INFO

↓

WARN

↓

ERROR

↓

FATAL
```

Not every framework supports all levels, but this ordering is common.

---

# TRACE

Most detailed.

Used for.

Development.

Examples.

- Variable values
- Method entry
- Method exit
- Internal flow

Usually disabled.

In production.

---

# DEBUG

Developer diagnostics.

Examples.

```text
Cache Lookup Started

Redis Response Received

Executing SQL
```

Useful.

During troubleshooting.

---

# INFO

Normal application behavior.

Examples.

```text
Application Started

Order Created

Payment Completed

User Logged In
```

Most production logs.

Are INFO.

---

# WARN

Something unexpected happened.

But application continues.

Examples.

```text
Retry Attempt

Cache Miss

Slow API

Configuration Warning
```

Requires attention.

But not immediate action.

---

# ERROR

Operation failed.

Examples.

```text
Database Timeout

Payment Failed

Kafka Publish Failed

Null Pointer Exception
```

Should trigger investigation.

---

# FATAL

Application cannot continue.

Examples.

```text
Cannot Start

Configuration Missing

Out Of Memory
```

Some logging frameworks map fatal conditions to ERROR instead of providing a separate FATAL level.

---

# Choosing Log Levels

| Situation | Level |
|------------|-------|
| Variable Value | TRACE |
| Debugging | DEBUG |
| Business Event | INFO |
| Recoverable Problem | WARN |
| Failed Operation | ERROR |
| Application Crash | FATAL / ERROR |

Use levels consistently.

---

# Common Mistake

Everything.

Logged.

As ERROR.

Result.

Alert fatigue.

Loss of signal.

Use appropriate levels.

---

# 19. Correlation ID

> **A Correlation ID is a unique identifier assigned to an entire request so that all related logs across multiple services can be linked together.**

This is one of the most important logging concepts.

In distributed systems.

---

# Why Correlation IDs?

Imagine.

One request.

Travels through.

```text
Gateway

↓

Order

↓

Inventory

↓

Payment

↓

Notification
```

Each service.

Produces logs.

How do we connect them?

---

# Without Correlation ID

```text
Order Log

Inventory Log

Payment Log

Notification Log
```

No relationship.

Searching becomes difficult.

---

# With Correlation ID

```text
Request

ABC123

↓

Gateway

↓

ABC123

↓

Order

↓

ABC123

↓

Inventory

↓

ABC123

↓

Payment

↓

ABC123
```

One identifier.

Connects.

Everything.

---

# Example

```json
{
 "correlationId":"ABC123",
 "service":"payment",
 "message":"Gateway Timeout"
}
```

Searching.

```
ABC123
```

Shows.

Entire request.

---

# Benefits

- Faster debugging
- Easier searching
- Root cause analysis
- Cross-service visibility

Every incoming request should receive a correlation ID.

---

# 20. Trace ID

Many engineers confuse.

Correlation ID.

And.

Trace ID.

---

# Correlation ID

Represents.

Business request.

Example.

```
Checkout
```

Across.

Entire platform.

---

# Trace ID

Represents.

Distributed trace.

Generated.

By tracing systems.

Such as OpenTelemetry.

---

# Relationship

```text
Customer Request

↓

Correlation ID

↓

Distributed Trace

↓

Trace ID

↓

Multiple Spans
```

Many organizations use the same identifier for both.

Others keep them separate.

---

# Example

```json
{
 "correlationId":"REQ-5001",
 "traceId":"4bf92f3577b34da6",
 "spanId":"00f067aa0ba902b7"
}
```

This enables deep debugging.

---

# 21. Centralized Logging

> **Centralized Logging collects logs from every application and stores them in a single searchable platform.**

Instead of.

Logging locally.

Every service.

Sends logs.

To one location.

---

# Architecture

```text
Application

↓

Log Agent

↓

Log Collector

↓

Central Logging

↓

Search

↓

Dashboard
```

---

# Enterprise Logging Platform

Example.

```text
Gateway

↓

Order

↓

Inventory

↓

Kafka

↓

Payment

↓

Kubernetes

↓

Central Logging Platform
```

Everything searchable.

In one place.

---

# Benefits

- Single source of truth
- Easy searching
- Cross-service debugging
- Security investigations
- Compliance

---

# Popular Logging Stack

Examples.

- ELK Stack (Elasticsearch, Logstash, Kibana)
- OpenSearch
- Grafana Loki
- Splunk
- Datadog Logs

Choice depends on scale, budget, and operational requirements.

---

# Log Search

Example.

Search.

```
OrderId=5001
```

Returns.

Logs.

From:

- Gateway
- Order Service
- Payment
- Kafka
- Notification

Entire request.

Visible.

---

# Log Retention

Logs consume significant storage.

Organizations define retention policies.

Example.

```text
Application Logs

30 Days

↓

Security Logs

1 Year

↓

Audit Logs

7 Years
```

Retention depends on compliance and business requirements.

---

# Log Rotation

Applications should rotate log files.

To prevent.

Disk exhaustion.

Typical strategies.

- Daily rotation
- Size-based rotation
- Compression
- Archival

---

# Log Sampling

Large systems generate.

Millions.

Of logs.

Per minute.

Sampling reduces volume.

Example.

```text
1000 INFO Logs

↓

Keep

100

↓

Discard

900
```

Errors.

Should usually not be sampled.

---

# Sensitive Data Masking

Sensitive information should be masked.

Before storage.

Example.

Instead of.

```text
4111111111111111
```

Store.

```text
************1111
```

Similarly.

Mask:

- Aadhaar numbers
- SSNs
- Tokens
- Email addresses (where appropriate)
- Phone numbers (where appropriate)

---

# Logging Performance

Logging should not significantly impact application performance.

Recommendations.

- Asynchronous logging
- Buffered writes
- Structured formats
- Avoid excessive logging inside tight loops

---

# Logging Best Practices

- Log meaningful events.
- Use structured logging.
- Include timestamps.
- Include service names.
- Include request identifiers.
- Include correlation IDs.
- Include trace IDs.
- Avoid duplicate logs.
- Mask sensitive data.
- Centralize storage.

---

# Common Mistakes

### Logging Sensitive Data

Passwords.

Secrets.

PII.

Should never appear in logs.

---

### Logging Everything

Too many logs.

Increase storage costs.

And slow investigations.

---

### Local Log Files Only

Logs disappear.

When containers restart.

Always centralize production logs.

---

### Missing Correlation IDs

Without request identifiers.

Cross-service debugging becomes extremely difficult.

---

### Using Print Statements

Production applications should use proper logging frameworks.

Not console output.

---

# Architect's Perspective

Logs provide the **story** behind production behavior.

Metrics tell you that a failure exists.

Traces show where it occurred.

Logs explain exactly what happened.

Architects should establish enterprise-wide logging standards covering:

- Structured formats
- Consistent log levels
- Correlation IDs
- Trace IDs
- Retention policies
- Sensitive data masking

Well-designed logging transforms production incidents from hours of manual investigation into minutes of targeted analysis.

---

**End of Part 4A**

The next part will cover:

- **Distributed Tracing**
- **Trace**
- **Span**
- **Parent & Child Spans**
- **Context Propagation**
- **OpenTelemetry**
- **Jaeger**
- **Zipkin**
- **Sampling Strategies**
