
# 27. Architecture Review Checklist

> **A fault-tolerant architecture review determines whether the system can continue delivering business value when failures inevitably occur.**

Unlike a functional review,

a fault tolerance review focuses on questions such as:

- What happens when components fail?
- Can failures propagate?
- Can customers continue working?
- Is recovery automatic?

The objective is:

> **"Can this system survive expected production failures?"**

---

# Business Review

Understand the business before reviewing technology.

## Business Questions

- [ ] Which business functions are mission critical?
- [ ] Which workflows cannot tolerate downtime?
- [ ] What are the Recovery Time Objective (RTO) requirements?
- [ ] What are the Recovery Point Objective (RPO) requirements?
- [ ] Which failures create financial loss?
- [ ] Which failures impact regulatory compliance?
- [ ] Which services require zero customer interruption?

---

# Architecture Review

Review the overall architecture.

Questions:

- [ ] Are there any Single Points of Failure?
- [ ] Are failure domains identified?
- [ ] Is redundancy implemented appropriately?
- [ ] Are services stateless where possible?
- [ ] Is automatic failover available?
- [ ] Are dependencies isolated?

---

# Application Review

Verify application-level fault tolerance.

Checklist:

- [ ] Global exception handling implemented?
- [ ] Timeouts configured?
- [ ] Retry policies defined?
- [ ] Retry budgets enforced?
- [ ] Circuit breakers implemented?
- [ ] Bulkheads configured?
- [ ] Graceful degradation available?
- [ ] Idempotency implemented?

---

# Database Review

Review database recovery capabilities.

Questions:

- [ ] Replication configured?
- [ ] Automatic failover tested?
- [ ] Replica lag monitored?
- [ ] Backup strategy documented?
- [ ] Recovery tested?
- [ ] WAL enabled?
- [ ] Point-in-Time Recovery supported?

---

# Messaging Review

For Kafka, RabbitMQ, SQS, or other messaging platforms.

Checklist:

- [ ] Dead Letter Queue configured?
- [ ] Retry policy defined?
- [ ] Poison message handling implemented?
- [ ] Consumer recovery tested?
- [ ] Producer acknowledgements configured?

---

# Kubernetes Review

Review platform resilience.

Checklist:

- [ ] Liveness probes configured?
- [ ] Readiness probes configured?
- [ ] Startup probes configured?
- [ ] ReplicaSets configured?
- [ ] Pod Disruption Budgets defined?
- [ ] Anti-affinity configured?
- [ ] Resource limits configured?

---

# Infrastructure Review

Questions:

- [ ] Multi-AZ deployment?
- [ ] Load balancers redundant?
- [ ] DNS failover configured?
- [ ] Auto Scaling enabled?
- [ ] Backup networking available?
- [ ] Capacity available after failures?

---

# Monitoring Review

Verify observability.

Monitor:

- [ ] Failure Detection Time
- [ ] MTTR
- [ ] MTBF
- [ ] Retry Count
- [ ] Circuit Breaker State
- [ ] Replica Lag
- [ ] Queue Depth
- [ ] Customer-visible Incidents

---

# Security Review

Failures often originate from security events.

Questions:

- [ ] Certificate expiration monitored?
- [ ] Secret rotation automated?
- [ ] Authentication service redundancy available?
- [ ] DDoS mitigation available?

---

# Review Outcome

| Objective | Status |
|-----------|--------|
| Failure Domains Identified | ✓ |
| SPOFs Removed | ✓ |
| Recovery Automated | ✓ |
| Monitoring Complete | ✓ |
| Capacity Validated | ✓ |
| Business Continuity Achieved | ✓ |

---

# 28. Production Readiness Checklist

Before deployment, architects validate fault tolerance assumptions.

```markdown
Business

- [ ] RTO documented
- [ ] RPO documented
- [ ] Business-critical workflows identified

Application

- [ ] Timeouts configured
- [ ] Retry strategy documented
- [ ] Circuit breakers enabled
- [ ] Bulkheads configured
- [ ] Graceful degradation implemented
- [ ] Idempotency verified

Database

- [ ] Replication enabled
- [ ] Automatic failover tested
- [ ] Replica lag monitored
- [ ] PITR configured
- [ ] Backups validated

Messaging

- [ ] DLQ configured
- [ ] Retry policy documented
- [ ] Consumer recovery tested

Infrastructure

- [ ] Multi-AZ deployment
- [ ] Load balancing verified
- [ ] DNS failover tested
- [ ] Auto Scaling enabled

Kubernetes

- [ ] Liveness probes
- [ ] Readiness probes
- [ ] Startup probes
- [ ] PDB configured
- [ ] Anti-affinity configured

Observability

- [ ] Metrics
- [ ] Logs
- [ ] Distributed tracing
- [ ] Alerts
- [ ] Dashboards

Testing

- [ ] Load Testing
- [ ] Chaos Testing
- [ ] Failover Testing
- [ ] Disaster Recovery Drill

Operations

- [ ] Runbooks completed
- [ ] On-call procedures documented
- [ ] Escalation matrix available
```

---

# 29. Architecture Decision Record (ADR)

Every major fault tolerance decision should be documented.

---

## ADR-005

### Title

Adopt Active-Passive Database Architecture with Automatic Failover

---

### Status

Accepted

---

### Context

The order management system currently depends on a single PostgreSQL primary database.

Business requirement:

```
Maximum Recovery Time

60 Seconds
```

Current recovery:

```
Manual

15 Minutes
```

Business impact:

- Order processing stops
- Payment failures
- Customer dissatisfaction

---

### Problem

The primary database is a Single Point of Failure.

---

### Decision

Implement:

- One Primary Database
- Two Replicas
- Automatic Failover
- Continuous Health Monitoring

---

### Alternatives Considered

#### Alternative 1

Scale vertically.

Rejected because:

- Does not remove SPOF.
- Hardware upgrades do not eliminate failures.

---

#### Alternative 2

Manual failover.

Rejected because:

- Recovery too slow.
- Human error risk.

---

### Consequences

Positive

- Lower MTTR
- Higher business continuity
- Automatic recovery

Negative

- Increased infrastructure cost
- Replica synchronization
- Operational complexity

---

### Success Criteria

- Failover < 30 Seconds
- Zero manual intervention
- Replica lag < 50 ms
- 99.99% availability

---

# 30. Fault Tolerance Thinking Framework

Experienced architects solve failures systematically.

---

# Step 1

Identify the business impact.

Questions:

- Which customers are affected?
- Which workflows stop?
- What revenue is at risk?

---

# Step 2

Identify the failure.

Examples:

- Hardware
- Software
- Network
- Database
- Third-party API

---

# Step 3

Locate the failure domain.

Questions:

- Is the failure isolated?
- Can it spread?
- Is there redundancy?

---

# Step 4

Select the appropriate recovery strategy.

Examples:

- Restart
- Retry
- Failover
- Circuit Breaker
- Graceful degradation

---

# Step 5

Measure recovery.

Metrics:

- Detection Time
- Failover Time
- MTTR
- Customer Impact

---

# Step 6

Improve architecture.

Every production incident should strengthen the next version of the system.

---

# Fault Tolerance Decision Tree

```text
Failure Detected?

↓

YES

↓

Component Healthy?

↓

NO

↓

Restart Possible?

↓

YES

↓

Restart

↓

Recovered?

↓

YES

↓

Continue

↓

NO

↓

Replica Available?

↓

YES

↓

Automatic Failover

↓

Recovered?

↓

YES

↓

Continue

↓

NO

↓

Graceful Degradation

↓

Notify Operations

↓

Root Cause Analysis
```

---

# 31. Architect's Mental Model

Experienced architects think differently about failures.

```text
Business Process

↓

Failure Scenario

↓

Customer Impact

↓

Failure Domain

↓

Recovery Strategy

↓

Automation

↓

Monitoring

↓

Continuous Improvement
```

Notice that the focus is never:

```
Server

↓

Restart
```

Instead,

the focus is:

```
Business

↓

Continues Operating
```

---

# Questions Every Architect Should Ask

Before approving production:

- What happens if this service crashes?
- What happens if an entire node fails?
- What happens if an availability zone becomes unavailable?
- What happens if the database becomes unreachable?
- Can recovery occur automatically?
- Can customers continue working?
- Is graceful degradation available?
- Is recovery tested regularly?
- Is monitoring sufficient?
- Will customers notice this failure?

---

# Fault Tolerance Review Cheat Sheet

| Question | Why It Matters |
|-----------|----------------|
| What fails first? | Identify weakest component |
| Is there a SPOF? | Remove business risk |
| Is failure isolated? | Prevent cascading failures |
| Is recovery automatic? | Reduce MTTR |
| Is failover tested? | Validate assumptions |
| Can customers continue? | Business continuity |
| Is data protected? | Prevent loss |
| Are retries safe? | Avoid retry storms |
| Are dependencies protected? | Prevent cascading failures |
| Is recovery measurable? | Continuous improvement |

---

# Fault Tolerance Optimization Priority

Always improve in this order:

```text
1. Identify Failure Domains

↓

2. Remove Single Points of Failure

↓

3. Detect Failures Quickly

↓

4. Automate Recovery

↓

5. Isolate Failures

↓

6. Add Graceful Degradation

↓

7. Validate Through Chaos Testing

↓

8. Continuously Improve
```

---

# Architect's Golden Rules

1. Assume every component will eventually fail.
2. Eliminate Single Points of Failure.
3. Detect failures before customers do.
4. Automate recovery whenever possible.
5. Isolate failures to prevent cascading outages.
6. Design graceful degradation instead of complete outages.
7. Validate recovery continuously through testing.
8. Every production incident should improve the architecture.
9. Business continuity is more important than component availability.
10. Fault tolerance is measured by customer impact, not by the number of failures.

---

**End of Part 8**

The final part will complete **Chapter 5 – Fault Tolerance** with:

- **Enterprise Case Studies (Amazon, Netflix, Google, Stripe, Uber, etc.)**
- **Architecture Diagrams**
- **Real Failure War Stories**
- **Interview Questions (Basic to Staff+ Architect)**
- **Common Interview Mistakes**
- **Best Practices**
- **Related Concepts**
- **Further Reading**
- **Revision Notes**
- **Chapter Summary**
- **Chapter Completion Checklist**
- **Architect's Final Principles**

This will conclude **Chapter 5 – Fault Tolerance** and naturally transition into **Chapter 6 – Resilience**.
