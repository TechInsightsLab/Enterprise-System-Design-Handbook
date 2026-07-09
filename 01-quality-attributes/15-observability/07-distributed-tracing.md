
# 22. Distributed Tracing

> **Distributed Tracing is the process of tracking a single request as it travels through multiple services in a distributed system.**

Distributed tracing answers one of the most important production questions:

> **Where exactly did this request spend its time?**

In modern architectures,

a single request rarely stays inside one application.

Instead, it travels across dozens of services.

---

# Why Distributed Tracing?

Consider an e-commerce checkout.

```text
Customer

↓

API Gateway

↓

Authentication

↓

Order Service

↓

Inventory Service

↓

Payment Service

↓

Kafka

↓

Notification Service

↓

Database
```

One request.

Eight services.

Without tracing,

finding performance bottlenecks becomes extremely difficult.

---

# Traditional Debugging

Suppose checkout takes.

```
8 Seconds
```

Without tracing.

Teams ask:

- Is Gateway slow?
- Is Order Service slow?
- Database?
- Kafka?
- Network?
- Payment Provider?

Hours.

May be spent.

Investigating.

---

# With Distributed Tracing

The trace shows.

```text
Gateway

25 ms

↓

Order

40 ms

↓

Inventory

18 ms

↓

Payment

7,650 ms

↓

Notification

12 ms
```

Root cause.

Immediately visible.

---

# What is a Trace?

> **A Trace represents the complete lifecycle of a single request across every service it touches.**

Every request.

Receives.

One unique Trace ID.

Example.

```text
Trace

4bf92f3577b34da6

↓

Entire Request
```

---

# Trace Example

```text
Customer

↓

Gateway

↓

Order

↓

Inventory

↓

Payment

↓

Database

↓

Response
```

Entire flow.

Captured.

Inside one trace.

---

# Trace Characteristics

A trace records:

- Request path
- Service dependencies
- Execution order
- Timing
- Errors
- Metadata

---

# Trace Lifecycle

```text
Request Arrives

↓

Trace Created

↓

Trace Propagated

↓

Spans Generated

↓

Trace Completed

↓

Observability Platform
```

---

# Enterprise Example

Customer places an order.

Trace records.

```text
Gateway

↓

Authentication

↓

Inventory

↓

Payment

↓

Fraud Check

↓

Kafka

↓

Notification
```

Entire business workflow.

Visible.

---

# 23. Span

> **A Span represents a single unit of work performed within a trace.**

Think of a trace.

As a movie.

A span.

Is one scene.

---

# Example

Checkout.

```text
Trace

↓

Gateway Span

↓

Order Span

↓

Inventory Span

↓

Payment Span

↓

Database Span
```

Each span.

Measures one operation.

---

# Span Contains

A span typically records.

- Start Time
- End Time
- Duration
- Service Name
- Operation Name
- Status
- Attributes
- Events

---

# Example

```text
Payment Service

Operation

Authorize Payment

Duration

420 ms

Status

Success
```

One span.

One operation.

---

# Nested Spans

Spans can contain.

Other spans.

Example.

```text
Checkout

↓

Payment

↓

Database

↓

Redis

↓

Fraud API
```

Each child operation.

Becomes its own span.

---

# Parent and Child Spans

Most requests contain.

Multiple spans.

Organized hierarchically.

---

# Example

```text
Checkout

(Parent)

↓

Inventory

(Child)

↓

Database

(Child)

↓

Redis

(Child)
```

Parent span.

Represents.

Business operation.

Child spans.

Represent dependencies.

---

# Trace Tree

```text
Checkout

├── Authentication
│
├── Order Service
│     │
│     ├── Database
│     └── Redis
│
├── Inventory
│     │
│     └── Database
│
└── Payment
      │
      ├── Fraud API
      └── Database
```

This hierarchy helps engineers understand request execution.

---

# Span Timing

Example.

```text
Gateway

20 ms

↓

Order

80 ms

↓

Database

40 ms

↓

Payment

620 ms
```

Payment.

Dominates latency.

Optimization target.

---

# 24. Context Propagation

> **Context Propagation is the mechanism by which trace information is passed between services so that all spans belong to the same trace.**

Without propagation,

every service creates.

Independent traces.

Impossible.

To reconstruct.

Entire request.

---

# Without Context Propagation

```text
Gateway

Trace A

↓

Order

Trace B

↓

Inventory

Trace C
```

Disconnected.

---

# With Context Propagation

```text
Gateway

Trace A

↓

Order

Trace A

↓

Inventory

Trace A

↓

Payment

Trace A
```

Entire request.

Unified.

---

# How It Works

The calling service sends.

Trace metadata.

Along with.

The request.

Example.

```http
traceparent:

00-4bf92f3577b34da6-00f067aa0ba902b7-01
```

The receiving service continues.

The same trace.

---

# Where Context Travels

Context propagates across.

- HTTP Headers
- gRPC Metadata
- Kafka Headers
- RabbitMQ Headers
- SQS Message Attributes
- Event Streams

Without propagation,

distributed tracing breaks.

---

# Example

Order Service.

Publishes.

Kafka Event.

Include.

Trace Context.

Notification Service.

Consumes.

Same trace.

Entire business workflow remains connected.

---

# 25. OpenTelemetry (OTel)

> **OpenTelemetry is the industry-standard open-source framework for generating, collecting, and exporting telemetry including metrics, logs, and traces.**

OpenTelemetry has become.

The de facto standard.

For observability instrumentation.

---

# Why OpenTelemetry?

Before OpenTelemetry.

Every monitoring vendor.

Required.

Different SDKs.

Example.

```text
Application

↓

Vendor A SDK

↓

Vendor B SDK

↓

Vendor C SDK
```

Changing vendors.

Required code changes.

---

# OpenTelemetry Solves This

```text
Application

↓

OpenTelemetry SDK

↓

Collector

↓

Any Backend
```

Instrumentation.

Becomes vendor neutral.

---

# OpenTelemetry Components

```text
OpenTelemetry

├── API
├── SDK
├── Collector
├── Auto Instrumentation
└── Exporters
```

Each component.

Has a specific responsibility.

---

# OpenTelemetry API

Defines.

Standard interfaces.

For instrumentation.

Applications.

Remain independent.

Of backend vendors.

---

# OpenTelemetry SDK

Implements.

Telemetry generation.

Responsible for.

- Metrics
- Logs
- Traces
- Context propagation

---

# OpenTelemetry Collector

> **The Collector is a vendor-neutral service that receives, processes, and exports telemetry.**

Instead of applications sending telemetry.

Directly.

To vendors.

They send.

To collector.

---

# Collector Architecture

```text
Application

↓

OpenTelemetry SDK

↓

Collector

↓

Prometheus

Jaeger

Grafana

Datadog

Splunk

New Relic
```

Collector acts.

As a central pipeline.

---

# Collector Benefits

- Vendor independence
- Central configuration
- Filtering
- Sampling
- Batching
- Security

---

# Auto Instrumentation

Many languages support.

Automatic instrumentation.

Without modifying application code.

Examples.

- Java Agent
- .NET Auto Instrumentation
- Python Auto Instrumentation
- Node.js Auto Instrumentation

Useful.

For existing applications.

---

# Manual Instrumentation

Business operations.

Should often be instrumented manually.

Example.

```text
Payment Approved

Coupon Applied

Loan Sanctioned

Policy Renewed
```

Automatic instrumentation cannot infer business semantics.

---

# 26. Jaeger

> **Jaeger is an open-source distributed tracing platform originally created by Uber for collecting, storing, and visualizing traces.**

Jaeger provides:

- Trace visualization
- Service dependency graphs
- Latency analysis
- Root cause investigation

---

# Jaeger Architecture

```text
Application

↓

OpenTelemetry

↓

Collector

↓

Jaeger

↓

UI
```

Engineers search.

By.

- Trace ID
- Service
- Duration
- Operation

---

# Example

Search.

```
Payment Service

Latency > 2 sec
```

Jaeger displays.

Relevant traces.

---

# 27. Zipkin

Zipkin is another.

Open-source.

Distributed tracing platform.

Provides.

- Trace storage
- Visualization
- Dependency analysis
- Latency breakdown

Historically.

Widely adopted.

Especially.

In Spring Cloud ecosystems.

---

# Jaeger vs Zipkin

| Jaeger | Zipkin |
|----------|---------|
| Uber Origin | Twitter Origin |
| Rich UI | Simpler UI |
| Strong Kubernetes Adoption | Mature Ecosystem |
| OpenTelemetry Support | OpenTelemetry Support |

Both remain valuable tracing backends.

---

# 28. Trace Sampling

Large enterprises process.

Millions.

Of requests.

Per second.

Storing every trace.

May become prohibitively expensive.

Sampling reduces telemetry volume.

---

# Why Sampling?

Suppose.

```text
5 Million Requests

Per Minute
```

Storing every trace.

Consumes enormous storage.

Sampling selects.

Only a subset.

---

# Types of Sampling

```text
Sampling

├── Always On
├── Always Off
├── Probabilistic
├── Rate Limited
├── Tail-Based
└── Adaptive
```

---

# Always On

Capture.

Every trace.

Advantages.

Maximum visibility.

Disadvantages.

Highest cost.

---

# Probabilistic Sampling

Capture.

Example.

```
10%

Of Requests
```

Random selection.

Simple.

Low overhead.

---

# Rate-Limited Sampling

Capture.

Fixed number.

Example.

```
100 Traces

Per Second
```

Useful.

During traffic spikes.

---

# Tail-Based Sampling

The decision to keep or discard a trace is made **after** the trace is observed.

This enables retention of:

- Slow requests
- Failed requests
- High-value business transactions

While discarding routine successful requests.

---

# Adaptive Sampling

Sampling rate changes dynamically.

Based on:

- Traffic volume
- Error rate
- System load

Provides a balance between visibility and cost.

---

# Sampling Strategy

Many enterprises adopt:

```text
100%

Errors

↓

100%

Slow Requests

↓

5%

Successful Requests
```

Excellent balance.

Between cost.

And visibility.

---

# Service Dependency Graph

Tracing platforms can automatically generate.

Dependency maps.

Example.

```text
Gateway

↓

Order

↓

Inventory

↓

Payment

↓

Kafka

↓

Notification
```

Architects gain visibility into real production dependencies.

---

# Common Mistakes

### No Context Propagation

Without context propagation,

distributed traces become fragmented.

---

### Tracing Only One Service

Tracing provides value only when the complete request path is visible.

Instrument all critical services.

---

### Ignoring External Dependencies

Database calls, third-party APIs, message brokers, and caches should all be traced.

---

### Always Sampling Everything

Full tracing may become prohibitively expensive at scale.

Choose an appropriate sampling strategy.

---

### Vendor-Locked Instrumentation

Prefer OpenTelemetry to reduce vendor lock-in and simplify migrations.

---

# Architect's Perspective

Distributed tracing is one of the most transformative capabilities in modern observability.

Metrics tell you **that latency increased**.

Logs explain **what failed**.

Tracing reveals **exactly where the request spent its time and which dependency caused the slowdown**.

For distributed systems, architects should ensure:

- Every request receives a Trace ID.
- Context propagates across all protocols.
- Critical business operations are instrumented.
- OpenTelemetry is used for vendor-neutral instrumentation.
- Sampling strategies balance operational insight with cost.

Without distributed tracing, debugging complex microservice architectures often becomes guesswork.

With tracing, production incidents that once required hours of investigation can often be diagnosed within minutes.

---

**End of Part 4B**

The next part will cover:

- **Monitoring Deep Dive**
- **White-Box Monitoring**
- **Black-Box Monitoring**
- **Health Checks**
- **Synthetic Monitoring**
- **Dashboards**
- **Alerting**
- **Alert Fatigue**
- **On-Call Engineering**
