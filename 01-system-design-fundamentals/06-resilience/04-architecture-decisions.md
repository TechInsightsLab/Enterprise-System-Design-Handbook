
# 21. Architecture Decisions

> **Resilience is determined by architectural decisions made long before the first production failure occurs.**

Most organizations don't become resilient during incidents.

They become resilient through the architecture they design months earlier.

Good resilience decisions answer questions like:

- How large should the blast radius be?
- Should recovery be automatic?
- Should deployments be progressive?
- How should traffic shift during failures?
- How should data survive regional outages?

These decisions determine how well systems recover.

---

# Architecture Decision Flow

```text
Business Objectives

↓

Risk Assessment

↓

Failure Analysis

↓

Architecture Decision

↓

Implementation

↓

Validation

↓

Continuous Improvement
```

Every decision should reduce business impact.

---

# 21.1 Shared Architecture vs Cell-Based Architecture

One of the biggest resilience decisions.

---

# Shared Architecture

Everything shares common infrastructure.

```text
Users

↓

Load Balancer

↓

Application Cluster

↓

Shared Database

↓

Shared Cache
```

---

## Advantages

- Simpler architecture
- Lower infrastructure cost
- Easier operations

---

## Disadvantages

Failure affects everyone.

```text
Database Failure

↓

Entire Platform Impacted
```

Large blast radius.

---

# Cell-Based Architecture

Independent environments.

```text
Cell A

↓

Database A

---------------

Cell B

↓

Database B

---------------

Cell C

↓

Database C
```

Each cell operates independently.

---

## Benefits

Failure remains localized.

```text
Cell A

↓

Failure

↓

Cells B & C Continue
```

---

## Trade-offs

- More infrastructure
- More operational complexity
- Deployment coordination

---

## Typical Use Cases

- Banking
- E-commerce
- SaaS platforms
- Large cloud providers

---

# Decision Guidelines

| Shared Platform | Cell-Based |
|-----------------|------------|
| Small systems | Large platforms |
| Lower cost | Higher resilience |
| Easier operations | Smaller blast radius |

---

# 21.2 Multi-Region Architecture

Availability Zones protect against localized failures.

Regions protect against disasters.

---

# Single Region

```text
Users

↓

Region A
```

---

## Risk

Regional outage:

```
Business Stops
```

---

# Multi-Region

```text
Global DNS

↓

US

Europe

Asia
```

Traffic shifts automatically.

---

# Benefits

- Regional disaster recovery
- Lower global latency
- Business continuity

---

# Challenges

- Data replication
- Higher infrastructure cost
- Distributed consistency
- Operational complexity

---

# Active-Active Multi-Region

All regions serve traffic.

```text
Users

↓

Global Load Balancer

↓

US

Europe

Asia
```

---

## Advantages

- Better utilization
- Faster failover
- Lower latency

---

## Challenges

- Conflict resolution
- Replication
- Strong consistency

---

# Active-Passive Multi-Region

Primary serves traffic.

Secondary waits.

```text
US

↓

Primary

-------------

Europe

↓

Standby
```

---

## Advantages

- Easier consistency
- Simpler operations

---

## Challenges

- Idle capacity
- Longer failover

---

# 21.3 Blast Radius Reduction

The objective is not eliminating failures.

The objective is reducing impact.

---

## Poor Design

```text
One Database

↓

One Cache

↓

One Queue

↓

Everything Depends
```

---

## Better Design

```text
Orders

↓

Database

Inventory

↓

Database

Payments

↓

Database
```

Business domains become isolated.

---

# Blast Radius Reduction Techniques

Examples:

- Cell architecture
- Separate databases
- Independent deployments
- Dedicated queues
- Service isolation
- Bulkheads

---

# 21.4 Recovery Automation

Manual recovery is rarely resilient.

---

## Manual

```text
Failure

↓

Engineer

↓

SSH

↓

Restart
```

---

## Automatic

```text
Failure

↓

Monitoring

↓

Policy

↓

Recovery

↓

Validation
```

---

# Automation Examples

Automatically:

- Restart pods
- Promote replicas
- Scale infrastructure
- Redirect traffic
- Notify engineers

---

# Benefits

- Lower MTTR
- Consistent recovery
- Reduced operational effort

---

# 21.5 Feature Flags

Feature Flags separate deployment from release.

---

## Example

AI Recommendations

↓

Disabled

↓

Application Continues

Instead of redeploying,

features can be disabled instantly.

---

# Benefits

- Reduce deployment risk
- Faster recovery
- Controlled rollout

---

# Feature Flag Workflow

```text
Deploy

↓

Feature Disabled

↓

Enable Gradually

↓

Disable Immediately

if Problems Occur
```

---

# 21.6 Dark Launch

Deploy functionality without exposing it to users.

---

Example:

```
New Search Engine

↓

Production

↓

Hidden

↓

Monitoring
```

Only after validation:

```
Visible to Users
```

---

# Benefits

- Lower deployment risk
- Real production validation
- Easier rollback

---

# 21.7 Canary Deployment

Deploy gradually.

Example:

```text
Version 1

95%

Version 2

5%
```

Monitor.

If healthy:

```
10%

↓

25%

↓

50%

↓

100%
```

---

# Benefits

- Small blast radius
- Safe production testing
- Easier rollback

---

# 21.8 Blue-Green Deployment

Maintain two identical environments.

```text
Blue

(Current)

Green

(New)
```

Switch traffic instantly.

---

# Rollback

```text
Problems?

↓

Return Traffic

↓

Blue
```

Very fast recovery.

---

# Advantages

- Near-zero downtime
- Simple rollback
- Lower deployment risk

---

# Trade-offs

- Duplicate infrastructure
- Higher cost

---

# 21.9 Progressive Delivery

Instead of releasing everywhere simultaneously:

```
Internal Users

↓

1%

↓

10%

↓

25%

↓

100%
```

Confidence increases gradually.

---

# Benefits

- Smaller failures
- Better monitoring
- Controlled exposure

---

# 21.10 Traffic Shifting

Route traffic dynamically.

Example:

```text
Healthy Region

80%

Recovering Region

20%
```

Adjust continuously.

---

# Common Strategies

- Weighted routing
- Latency routing
- Geographic routing
- Health-based routing

---

# 21.11 Disaster Recovery Strategy

Resilience requires disaster planning.

---

# Cold Standby

Infrastructure created after disaster.

Advantages:

- Low cost

Disadvantages:

- Slow recovery

---

# Warm Standby

Infrastructure running.

Limited capacity.

Recovery:

Fast.

---

# Hot Standby

Fully operational.

Traffic switches immediately.

Highest resilience.

Highest cost.

---

# Disaster Recovery Comparison

| Strategy | Recovery Speed | Cost |
|-----------|---------------|------|
| Cold | Slow | Low |
| Warm | Moderate | Medium |
| Hot | Fast | High |

---

# 21.12 Recovery Prioritization

Recover services according to business value.

---

Example

```text
Authentication

↓

Payments

↓

Orders

↓

Inventory

↓

Search

↓

Recommendations
```

Not every service deserves identical recovery priority.

---

# Priority Matrix

| Priority | Examples |
|-----------|----------|
| Critical | Authentication, Payments |
| High | Orders, Inventory |
| Medium | Search |
| Low | Recommendations, Analytics |

---

# 21.13 Continuous Validation

Architectures drift over time.

Recovery procedures must remain valid.

Examples:

- Monthly failover tests
- Quarterly DR drills
- Weekly chaos experiments
- Daily synthetic monitoring

Validation should be continuous.

---

# 21.14 Architecture Decision Matrix

| Business Requirement | Recommended Decision |
|----------------------|----------------------|
| Minimize Blast Radius | Cell Architecture |
| Regional Recovery | Multi-Region |
| Fast Rollback | Blue-Green Deployment |
| Safe Deployment | Canary Release |
| Progressive Exposure | Progressive Delivery |
| Instant Feature Control | Feature Flags |
| Automatic Recovery | Self-Healing |
| Disaster Recovery | Hot/Warm Standby |
| Global Customers | Traffic Shifting |
| Continuous Confidence | Chaos Engineering |

---

# Architecture Comparison

| Decision | Primary Benefit | Trade-off |
|-----------|-----------------|-----------|
| Cell Architecture | Small Blast Radius | Operational Complexity |
| Multi-Region | Disaster Resilience | High Cost |
| Active-Active | Fast Recovery | Consistency Complexity |
| Active-Passive | Simplicity | Idle Capacity |
| Canary | Safe Deployment | Longer Rollout |
| Blue-Green | Instant Rollback | Duplicate Infrastructure |
| Feature Flags | Operational Flexibility | Feature Management |
| Progressive Delivery | Lower Risk | Deployment Complexity |
| Traffic Shifting | Dynamic Recovery | Routing Complexity |
| Hot Standby | Immediate Recovery | Highest Cost |

---

# Architect's Perspective

Resilient architecture is the result of **many coordinated decisions**, not one technology.

A modern resilient platform often combines:

- Cell-based architecture
- Multi-region deployment
- Active-Active services
- Automatic recovery
- Feature Flags
- Canary deployments
- Blue-Green releases
- Progressive delivery
- Traffic shifting
- Continuous disaster recovery validation

Each decision contributes to one goal:

> **Ensure that failures remain small, recovery is fast, deployments are safe, and business operations continue without significant customer impact.**


---

**End of Part 4**

The next section will cover **Implementation Mechanisms**, including:

- Kubernetes resilience
- Spring Boot resilience
- Service Mesh resilience
- Kafka resilience
- Database resilience
- Cloud-native resilience (AWS, Azure, GCP)
- Auto Scaling
- Self-Healing Infrastructure
- Chaos Engineering Tooling
- Resilience Implementation Decision Matrix
