
# 7. Resilience Patterns

Distributed systems.

Will fail.

This is not.

A possibility.

It is.

A certainty.

Servers crash.

Networks fail.

Databases become slow.

Cloud regions go offline.

Third-party APIs stop responding.

Hardware fails.

Software contains bugs.

---

# What Makes a System Resilient?

> **Resilience is the ability of a system to continue delivering acceptable service despite failures, overload, or unexpected conditions.**

Notice.

Not.

"Never fail."

Instead.

"Continue operating."

Even.

When failures occur.

---

# Why Resilience Patterns Exist

Imagine.

An e-commerce platform.

```text
Customer

↓

API Gateway

↓

Order Service

↓

Payment Service

↓

Inventory Service

↓

Shipping Service
```

Question.

What happens.

If.

Payment Service.

Stops responding?

Should.

Everything fail?

Or.

Should.

Only payment.

Be affected?

Without resilience.

One failure.

Can.

Bring down.

The entire platform.

---

# Business Story

Imagine.

Netflix.

Streams.

Millions.

Of videos.

Recommendation Service.

Suddenly crashes.

Should.

Customers.

Be unable.

To watch movies?

Of course not.

Recommendations.

Can disappear.

Streaming.

Must continue.

This.

Is resilience.

---

# Classification

```text
Resilience Patterns

│

├── Timeout

├── Retry

├── Circuit Breaker

├── Bulkhead

├── Fallback

├── Health Check

├── Load Shedding

├── Rate Limiting

├── Backpressure

└── Graceful Degradation
```

Each pattern.

Addresses.

A different.

Failure mode.

---

# Failure Cascade

Without resilience.

One failure.

Propagates.

Throughout the system.

```text
Payment

↓

Order

↓

Checkout

↓

Mobile App

↓

Customer
```

This is called.

A.

**Cascading Failure.**

---

# Goal

Stop.

Failures.

From spreading.

---

# 7.1 Timeout Pattern

The simplest.

Yet.

Most important.

Resilience pattern.

---

# Problem

Service A.

Calls.

Service B.

```text
Order Service

↓

Payment Service
```

Payment.

Never responds.

Question.

How long.

Should.

Order Service.

Wait?

Forever?

---

Never.

Wait forever.

---

# Definition

> **A Timeout limits how long a service waits for another service before abandoning the request.**

---

# Architecture

```text
Order Service

↓

Payment Service

↓

3 Seconds

↓

Timeout

↓

Failure
```

---

# Why Timeouts Matter

Without timeout.

Threads remain blocked.

Memory fills.

Connection pools exhaust.

Entire application.

Stops responding.

---

# Good Timeout Strategy

```text
Database

↓

100 ms

--------------------

Cache

↓

20 ms

--------------------

Internal Service

↓

300 ms

--------------------

External API

↓

2–5 sec
```

Timeouts.

Should reflect.

Business expectations.

---

# Anti-Pattern

```text
Timeout = 5 Minutes
```

Equivalent to.

No timeout.

---

# 7.2 Retry Pattern

Some failures.

Are temporary.

---

Example.

```text
HTTP 503

↓

Service Restarting
```

Retry.

May succeed.

---

# Definition

> **Retry automatically attempts an operation again after a temporary failure.**

---

# Architecture

```text
Request

↓

Failure

↓

Retry

↓

Success
```

---

# Example

Payment Gateway.

Returns.

```text
503
```

Retry.

One second later.

Succeeds.

---

# Retry Strategy

Bad.

```text
Retry Immediately

Retry Immediately

Retry Immediately
```

Creates.

Retry Storm.

---

Better.

Exponential Backoff.

```text
1 sec

↓

2 sec

↓

4 sec

↓

8 sec
```

---

# Retry + Jitter

If.

Thousands.

Of clients.

Retry.

Exactly.

At.

The same time.

They create.

Another traffic spike.

---

Solution.

Random delay.

```text
4.2 sec

3.8 sec

4.7 sec

5.1 sec
```

Called.

**Jitter.**

---

# When NOT to Retry

Never retry.

Business failures.

Example.

```text
Invalid Password

Insufficient Funds

Invalid Card

Permission Denied
```

Retries.

Will never help.

---

# 7.3 Circuit Breaker Pattern

One of the most.

Important.

Microservice patterns.

---

# Problem

Suppose.

Payment Service.

Is down.

Without Circuit Breaker.

Every request.

Still attempts.

Payment.

```text
Customer

↓

Order

↓

Payment

↓

Timeout

↓

Failure
```

Thousands.

Of useless requests.

Increase.

Load.

On an already.

Broken service.

---

# Definition

> **A Circuit Breaker stops sending requests to an unhealthy service after repeated failures, allowing it time to recover.**

---

# Electrical Analogy

House.

Has.

Circuit breaker.

When overload occurs.

Electricity.

Stops.

Protecting.

The house.

Software.

Uses.

The same idea.

---

# States

```text
Circuit Breaker

│

├── Closed

├── Open

└── Half-Open
```

---

# Closed State

Everything.

Healthy.

```text
Requests

↓

Service
```

All traffic.

Flows normally.

---

# Open State

Too many failures.

```text
Requests

↓

Rejected Immediately
```

No requests.

Reach.

The failed service.

---

# Half-Open State

After.

Cooling period.

Allow.

A few requests.

If successful.

Close.

Circuit.

If not.

Open again.

---

# Flow

```text
Success

↓

Closed

↓

Failures

↓

Open

↓

Cooldown

↓

Half-Open

↓

Healthy?

↓

Closed
```

---

# Benefits

- Prevents cascading failures
- Protects unhealthy services
- Faster failure detection
- Improves recovery

---

# 7.4 Bulkhead Pattern

Previously covered.

In detail.

Now.

Let's see.

Its role.

Inside.

Resilience.

---

# Definition

> **Bulkhead isolates resources so that failure in one workload does not affect others.**

---

# Ship Analogy

Ships.

Have.

Waterproof compartments.

Flooding.

One compartment.

Doesn't sink.

Entire ship.

---

# Software

```text
Payments

↓

Dedicated Thread Pool

----------------------

Search

↓

Dedicated Thread Pool

----------------------

Notifications

↓

Dedicated Thread Pool
```

Search.

Cannot.

Consume.

Payment resources.

---

# Benefits

- Resource isolation
- Fault containment
- Better availability

---

# 7.5 Fallback Pattern

Sometimes.

The best response.

Is.

A reduced response.

Not.

An error.

---

# Definition

> **Fallback provides an alternative response when the primary operation fails.**

---

Example.

Recommendation Service.

Fails.

Instead of.

```text
500 Internal Server Error
```

Return.

```text
Popular Products
```

Customer.

Still shops.

---

# Business Example

Google Maps.

Traffic service.

Fails.

Show.

Map.

Without traffic.

---

Netflix.

Recommendation.

Fails.

Continue.

Streaming.

Movies.

---

# 7.6 Health Check Pattern

How.

Does Kubernetes.

Know.

A service.

Is healthy?

Health checks.

---

# Definition

> **Health Checks determine whether an application is healthy enough to receive traffic.**

---

Types.

```text
Health Checks

│

├── Liveness

├── Readiness

└── Startup
```

---

# Liveness

Question.

```text
Should Kubernetes

restart

the application?
```

---

# Readiness

Question.

```text
Can the application

receive traffic?
```

---

# Startup

Question.

```text
Has the application

finished

starting?
```

Useful.

For.

Large JVM applications.

---

# 7.7 Load Shedding

Sometimes.

Traffic.

Exceeds.

Capacity.

---

Question.

Process.

Everything?

Or.

Protect.

The system?

---

Definition.

> **Load Shedding intentionally rejects excess requests to keep the system operational.**

---

Example.

Black Friday.

Capacity.

```text
100,000 req/sec
```

Incoming.

```text
250,000 req/sec
```

Reject.

150,000.

Protect.

100,000.

---

Better.

Some customers.

Receive.

```text
429 Too Many Requests
```

Than.

Everyone.

Receives.

```text
500 Internal Server Error
```

---

# 7.8 Rate Limiting

Rate Limiting.

Controls.

Who.

Can consume.

Resources.

---

Example.

```text
100 Requests

Per Minute

Per User
```

Protects.

Against.

Bots.

Abuse.

Traffic spikes.

---

Unlike.

Load Shedding.

Rate Limiting.

Is proactive.

Load Shedding.

Is reactive.

---

# 7.9 Backpressure

Previously.

We discussed.

Queues.

Question.

What if.

Consumers.

Cannot keep up?

---

Producer.

```text
1000 msgs/sec
```

Consumer.

```text
100 msgs/sec
```

Queue.

Keeps growing.

Eventually.

Memory.

Runs out.

---

Definition.

> **Backpressure slows producers when consumers cannot process data fast enough.**

---

Example.

Kafka.

Reactive Streams.

Akka Streams.

Implement.

Backpressure.

Automatically.

---

# 7.10 Graceful Degradation

Not every feature.

Has.

Equal importance.

---

Example.

E-commerce.

Core.

```text
Checkout
```

Optional.

```text
Recommendations

Recently Viewed

Coupons

Ratings
```

During overload.

Disable.

Optional features.

Checkout.

Continues.

---

# Architecture

```text
High Load

↓

Disable

Recommendations

↓

Disable

Analytics

↓

Checkout

Still Works
```

---

# Combining Patterns

Production systems.

Rarely use.

One resilience pattern.

---

Example.

```text
Client

↓

API Gateway

↓

Timeout

↓

Retry

↓

Circuit Breaker

↓

Bulkhead

↓

Fallback

↓

Response
```

Every request.

Passes.

Multiple layers.

Of protection.

---

# Pattern Comparison

| Pattern | Solves |
|----------|---------|
| Timeout | Infinite waiting |
| Retry | Temporary failures |
| Circuit Breaker | Repeated failures |
| Bulkhead | Resource isolation |
| Fallback | Reduced functionality |
| Health Check | Failure detection |
| Load Shedding | Overload protection |
| Rate Limiting | Abuse prevention |
| Backpressure | Slow consumers |
| Graceful Degradation | Partial service availability |

---

# Common Misconceptions

### Misconception 1

> Retry should always be enabled.

False.

Retrying permanent business failures wastes resources and may amplify traffic.

---

### Misconception 2

> Circuit Breaker replaces retries.

False.

Retries handle transient failures.

Circuit Breakers stop repeated requests to persistently unhealthy services.

They complement each other.

---

### Misconception 3

> Bulkhead only applies to threads.

False.

Bulkheads can isolate:

- Thread pools
- Connection pools
- CPU
- Memory
- Network bandwidth
- Message consumers
- Kubernetes resource quotas

---

### Misconception 4

> Graceful degradation means returning incorrect data.

False.

It means temporarily disabling non-essential functionality while preserving core business operations.

---

# Interview Questions

### Q1

Why should every remote service call have a timeout?

**Answer**

Without timeouts, blocked requests consume threads, memory, and connections indefinitely, eventually causing resource exhaustion and cascading failures.

---

### Q2

When should retries be avoided?

**Answer**

Retries should not be used for permanent business failures such as validation errors, insufficient funds, authentication failures, or invalid requests.

---

### Q3

Why is Circuit Breaker used together with Retry?

**Answer**

Retries recover from temporary failures, while Circuit Breakers prevent repeated requests from overwhelming persistently failing services.

---

### Q4

What is the difference between Rate Limiting and Load Shedding?

**Answer**

Rate Limiting proactively controls request rates based on policies, while Load Shedding reactively rejects excess traffic when system capacity is exceeded.

---

# Architect's Perspective

Resilience is not achieved through a single pattern.

It emerges from **multiple defensive layers** working together.

A production-grade distributed system typically combines **Timeouts**, **Retries**, **Circuit Breakers**, **Bulkheads**, **Health Checks**, **Rate Limiting**, **Backpressure**, and **Graceful Degradation** to prevent localized failures from escalating into system-wide outages.

Architects should design for failure from the beginning, assuming that every dependency will eventually become slow, unavailable, or overloaded.

---

**End of Part 7**

The next part will cover **Scalability Patterns**, including:

- Cache Aside
- Read Through Cache
- Write Through Cache
- Write Behind Cache
- Refresh Ahead Cache
- Read Replicas
- Sharding
- Partitioning
- CDN
- Geo-Replication
- Horizontal Scaling strategies
