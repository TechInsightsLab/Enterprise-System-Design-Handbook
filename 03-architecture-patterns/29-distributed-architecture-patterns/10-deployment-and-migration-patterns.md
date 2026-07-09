
# 10. Deployment & Migration Patterns

Building.

A great system.

Is only.

Half.

The challenge.

The other half.

Is deploying.

New versions.

Safely.

Without.

Breaking production.

---

# Why Deployment Patterns Exist

Imagine.

Amazon.

Deploys.

A new version.

Every day.

Millions.

Of customers.

Are shopping.

Question.

Should Amazon.

Stop.

The website.

Deploy.

Then.

Start again?

Impossible.

Modern systems.

Require.

Zero downtime.

Deployments.

---

# Business Story

Suppose.

Netflix.

Has.

250 million users.

A deployment.

Introduces.

A serious bug.

Without.

Deployment strategies.

```text
Deploy

↓

Entire Platform

Fails
```

Millions.

Of users.

Affected.

Revenue.

Lost.

Deployment patterns.

Reduce.

This risk.

---

# Classification

```text
Deployment & Migration Patterns

│

├── Rolling Deployment

├── Blue-Green Deployment

├── Canary Deployment

├── Feature Flags

├── Strangler Fig

├── Branch by Abstraction

└── Parallel Run
```

These patterns.

Answer.

Two questions.

1. How do we deploy safely?
2. How do we migrate safely?

---

# Choosing a Deployment Pattern

Ask.

```text
Need zero downtime?

↓

Rolling Deployment

────────────────────

Need instant rollback?

↓

Blue-Green

────────────────────

Need gradual rollout?

↓

Canary

────────────────────

Need hidden features?

↓

Feature Flags

────────────────────

Need monolith migration?

↓

Strangler Fig

────────────────────

Need internal refactoring?

↓

Branch by Abstraction

────────────────────

Need business validation?

↓

Parallel Run
```

---

# 10.1 Rolling Deployment

The most common.

Deployment strategy.

In Kubernetes.

---

# Definition

> **Rolling Deployment gradually replaces old application instances with new ones while keeping the application available.**

---

# Architecture

Before.

```text
Version 1

Pod 1

Pod 2

Pod 3

Pod 4
```

Deployment.

```text
Pod 1

↓

Version 2

↓

Pod 2

↓

Version 2

↓

...
```

Eventually.

```text
Version 2

Pod 1

Pod 2

Pod 3

Pod 4
```

---

# Advantages

- Zero downtime
- Low infrastructure cost
- Automatic rollout
- Simple

---

# Challenges

During deployment.

Both versions.

May run.

Simultaneously.

Applications.

Must be.

Backward compatible.

---

# Best Practices

- Use readiness probes.
- Deploy gradually.
- Monitor error rates.
- Automate rollback.

---

# 10.2 Blue-Green Deployment

One of the safest.

Deployment patterns.

---

# Definition

> **Blue-Green Deployment maintains two identical production environments. One serves live traffic while the other receives the new version. Traffic switches only after validation.**

---

# Architecture

```text
           Load Balancer

               │

      ┌────────┴────────┐

      ▼                 ▼

 Blue Environment   Green Environment

(Current)          (New Version)
```

---

# Deployment

Initially.

```text
100%

↓

Blue
```

Deploy.

Version 2.

To.

Green.

Test.

Green.

Switch.

Traffic.

```text
100%

↓

Green
```

Rollback.

Simply.

Switch.

Back.

To.

Blue.

---

# Advantages

- Instant rollback
- No downtime
- Safe production testing
- Easy validation

---

# Drawbacks

- Double infrastructure cost
- Database migrations.
Must be handled carefully.

---

# Real Companies

- Amazon
- Netflix
- Azure
- Banking platforms

---

# 10.3 Canary Deployment

Sometimes.

We don't trust.

A new release.

Enough.

To expose.

Everyone.

---

# Definition

> **Canary Deployment gradually exposes a new version to a small percentage of users before increasing traffic based on observed health.**

---

# Architecture

```text
Traffic

↓

95%

Version 1

──────────────

5%

Version 2
```

If healthy.

```text
90%

↓

10%

↓

70%

↓

30%

↓

50%

↓

50%

↓

100%
```

---

# Why "Canary"?

Coal miners.

Used.

Canary birds.

To detect.

Dangerous gases.

Before.

Humans.

Entered.

The mine.

Similarly.

A small group.

Of users.

Detects.

Production issues.

Before.

Everyone.

Is affected.

---

# Advantages

- Reduced deployment risk
- Real user validation
- Easy monitoring
- Gradual rollout

---

# Challenges

- Traffic management
- Version compatibility
- Monitoring complexity

---

# Typical Metrics

Monitor.

- Error rate
- Latency
- CPU
- Memory
- Business KPIs
- Conversion rate
- Checkout success

If metrics degrade.

Stop rollout.

Immediately.

---

# 10.4 Feature Flags

Sometimes.

Deployment.

Should not.

Automatically.

Enable.

A feature.

---

# Definition

> **Feature Flags allow functionality to be enabled or disabled at runtime without redeploying the application.**

---

# Architecture

```text
Application

↓

Feature Flag

↓

Enabled?

↓

Yes

↓

New Feature

──────────────

No

↓

Old Feature
```

---

# Example

Black Friday.

New checkout.

Code.

Already deployed.

But.

Flag.

Disabled.

Business.

Enables.

Feature.

Gradually.

---

# Benefits

- Safe releases
- Instant rollback
- A/B testing
- Beta users
- Progressive delivery

---

# Challenges

Old flags.

Should.

Be removed.

Otherwise.

Technical debt.

Accumulates.

---

# 10.5 Strangler Fig Pattern

Previously.

Discussed.

Briefly.

Now.

Let's explore.

Migration.

---

# Problem

Large.

Monolith.

Cannot.

Be rewritten.

All at once.

---

# Definition

> **The Strangler Fig Pattern gradually replaces parts of a legacy application with new services until the legacy system can be retired.**

---

# Architecture

Initially.

```text
Users

↓

Monolith
```

Step 1.

```text
Users

↓

API Gateway

↓

Monolith

↓

Product Service
```

Step 2.

```text
Orders

↓

Microservice
```

Step 3.

```text
Payments

↓

Microservice
```

Eventually.

```text
API Gateway

↓

Microservices
```

Legacy removed.

---

# Why the Name?

A.

Strangler Fig.

Tree.

Grows.

Around.

Another tree.

Eventually.

Replacing it.

Without.

Sudden destruction.

---

# Advantages

- Low migration risk
- Incremental modernization
- Continuous business operation

---

# Challenges

- Hybrid architecture
- Temporary duplication
- Long migration period

---

# 10.6 Branch by Abstraction

Sometimes.

Need.

To replace.

A component.

Without.

Large code changes.

---

# Definition

> **Branch by Abstraction introduces an abstraction layer so old and new implementations can coexist while the migration is performed gradually.**

---

# Architecture

```text
Application

↓

Interface

↓

Old Implementation

↓

New Implementation
```

Traffic.

Gradually shifts.

---

# Example

Database migration.

```text
Payment Interface

↓

Oracle

↓

PostgreSQL
```

Later.

Remove.

Oracle.

---

# Advantages

- Safer refactoring
- Reduced merge conflicts
- Incremental replacement

---

# 10.7 Parallel Run

Sometimes.

Business.

Needs.

Proof.

Before.

Replacing.

Critical systems.

---

# Definition

> **Parallel Run executes the old and new systems simultaneously, comparing outputs before switching production traffic.**

---

# Architecture

```text
Request

↓

Old System

↓

Result

────────────

Request

↓

New System

↓

Result

↓

Compare
```

Only after.

Results match.

Production.

Moves.

To new system.

---

# Common Uses

- Banking
- Insurance
- Trading
- Healthcare
- Government

Anywhere.

Incorrect results.

Are unacceptable.

---

# Advantages

- Very low business risk
- Confidence before migration
- Easy comparison

---

# Challenges

- Double infrastructure
- Duplicate processing
- Higher operational cost

---

# Pattern Relationships

Large organizations.

Combine.

Multiple patterns.

---

Example.

Monolith migration.

```text
Monolith

↓

Strangler Fig

↓

Microservices

↓

Feature Flags

↓

Canary

↓

Blue-Green

↓

Rolling Deployment
```

Each pattern.

Reduces.

A different.

Type of risk.

---

# Example — Netflix Deployment

```text
Developer

↓

CI/CD Pipeline

↓

Feature Flag

↓

Canary

↓

Monitoring

↓

Blue-Green

↓

Production
```

Multiple.

Deployment strategies.

Work together.

---

# Deployment Decision Matrix

| Requirement | Pattern |
|-------------|----------|
| Zero downtime | Rolling Deployment |
| Instant rollback | Blue-Green |
| Gradual exposure | Canary |
| Runtime control | Feature Flags |
| Legacy modernization | Strangler Fig |
| Safe refactoring | Branch by Abstraction |
| Validate business correctness | Parallel Run |

---

# Common Misconceptions

### Misconception 1

> Rolling Deployment guarantees zero risk.

False.

It minimizes downtime but still requires compatibility between old and new versions.

---

### Misconception 2

> Blue-Green Deployment eliminates all deployment problems.

False.

Database schema changes, external integrations, and data migrations still require careful planning.

---

### Misconception 3

> Feature Flags replace deployment strategies.

False.

Feature Flags control functionality, while deployment strategies control how software versions reach production.

---

### Misconception 4

> Strangler Fig is only for monolith-to-microservice migrations.

False.

It can be used to incrementally replace any large subsystem or legacy component.

---

# Interview Questions

### Q1

When would you choose Canary Deployment over Blue-Green Deployment?

**Answer**

Canary Deployment is preferred when gradual exposure to real users is needed to validate application health and business metrics before a full rollout. Blue-Green is preferred when rapid switching and instant rollback are more important.

---

### Q2

Why are Feature Flags valuable?

**Answer**

They decouple deployment from feature release, allowing new functionality to be enabled gradually, rolled back instantly, or tested with selected users.

---

### Q3

What problem does the Strangler Fig Pattern solve?

**Answer**

It enables incremental modernization of legacy systems without requiring a risky full rewrite or extended downtime.

---

### Q4

When is Parallel Run justified despite its high cost?

**Answer**

For mission-critical systems where incorrect results could have severe financial, legal, or safety consequences, such as banking, healthcare, or trading platforms.

---

# Architect's Perspective

Deployment and migration are **business risk management activities**, not merely DevOps tasks.

The goal is not to deploy software faster; it is to deliver change **safely**, **predictably**, and **reversibly**.

Experienced architects combine deployment strategies, feature management, observability, and gradual migration techniques to minimize operational risk while enabling continuous delivery.

---

**End of Part 10**

The next part will conclude the chapter with:

- Architecture Pattern Selection Framework
- Pattern Relationship Map
- Common Anti-Patterns
- Real-World Architecture Case Studies (Amazon, Netflix, Uber)
- Architecture Decision Matrix
- Interview Cheat Sheet
- Chapter Summary
