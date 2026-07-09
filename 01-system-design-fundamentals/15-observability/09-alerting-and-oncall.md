
# 36. Alerting

> **Alerting is the process of automatically notifying engineers when predefined conditions indicate that a system requires attention.**

Monitoring detects abnormal behavior.

Alerting ensures.

The right people.

Know about it.

At the right time.

Without alerting.

Production issues may remain unnoticed.

Until customers complain.

---

# Why Alerting Matters

Imagine.

A payment service.

Stops processing payments.

At.

2:00 AM.

Monitoring detects.

```
Payment Errors

↑
```

Without alerting.

Nobody notices.

For several hours.

Revenue is lost.

---

With alerting.

```text
Payment Errors

↓

Alert

↓

On-Call Engineer

↓

Investigation

↓

Recovery
```

Incident duration.

Reduced dramatically.

---

# Alert Lifecycle

Every alert follows a lifecycle.

```text
Telemetry

↓

Rule Evaluation

↓

Threshold Breached

↓

Alert Created

↓

Notification

↓

Engineer

↓

Acknowledged

↓

Resolved

↓

Closed
```

This lifecycle should be automated.

---

# Components of Alerting

```text
Metrics

↓

Alert Rules

↓

Alert Manager

↓

Notification

↓

On-Call Engineer
```

The monitoring platform evaluates telemetry continuously.

---

# Good Alert Characteristics

An alert should be:

- Actionable
- Accurate
- Timely
- Relevant
- Prioritized
- Easy to understand

If an engineer cannot determine what action to take,

the alert is poorly designed.

---

# Types of Alerts

Enterprise systems typically generate alerts for:

```text
Availability

Latency

Errors

Capacity

Security

Infrastructure

Business

Compliance
```

Each category serves a different operational purpose.

---

# Infrastructure Alerts

Examples.

```text
CPU > 90%

Memory > 95%

Disk > 85%

Node Down

Pod Restart Loop

Kafka Broker Down
```

Infrastructure alerts protect platform health.

---

# Application Alerts

Examples.

```text
HTTP 500 Errors

Latency > 2 Seconds

Database Timeout

Authentication Failure

Cache Miss Spike
```

These protect application reliability.

---

# Business Alerts

Examples.

```text
Orders Dropped

Payments Failed

Revenue Decreased

Checkout Success Rate Declined

Login Success Below 95%
```

Business alerts often have greater organizational impact than infrastructure alerts.

---

# Security Alerts

Examples.

```text
Failed Login Spike

Privilege Escalation

Secret Access

Suspicious API Usage

MFA Disabled
```

These should integrate with the organization's security operations.

---

# Alert Severity

Not every alert has the same importance.

A common classification.

```text
Critical

↓

High

↓

Medium

↓

Low

↓

Informational
```

Severity determines urgency.

---

# Critical

Examples.

- Entire application unavailable
- Payment processing stopped
- Database unavailable
- Customer-facing outage

Requires immediate action.

---

# High

Examples.

- High error rate
- High latency
- Kafka lag increasing rapidly
- Cache unavailable

Action required.

Soon.

---

# Medium

Examples.

- CPU consistently high
- Disk approaching capacity
- Slow background jobs

Plan remediation.

Before business impact.

---

# Low

Examples.

- Certificate expires in 30 days
- Backup delayed
- Increased retry count

Useful for maintenance.

Not emergencies.

---

# Informational

Examples.

- Deployment completed
- Autoscaling occurred
- Scheduled maintenance started

Provides operational awareness.

Without requiring action.

---

# Threshold-Based Alerting

The simplest alert type.

```text
CPU

>

90%

↓

Alert
```

Easy to implement.

But may generate false positives.

---

# Static Threshold Example

```text
Latency

>

500 ms

↓

Alert
```

Simple.

Widely used.

---

# Dynamic Alerting

Static thresholds.

Do not always work.

Traffic varies.

Throughout the day.

Dynamic alerting adapts.

Based on historical behavior.

Example.

```text
Current Latency

↓

Historical Baseline

↓

Deviation

↓

Alert
```

Better for variable workloads.

---

# Rate-Based Alerts

Rather than alerting.

On totals.

Alert on rates.

Example.

```text
HTTP Errors

5%

↓

Alert
```

Instead of.

```text
100 Errors
```

Percentages often provide better operational context.

---

# Consecutive Failure Alerts

Avoid alerting.

On a single failure.

Example.

```text
Health Check

Fail

↓

Retry

↓

Retry

↓

Retry

↓

Alert
```

This reduces noise.

From transient issues.

---

# Composite Alerts

A composite alert combines multiple signals.

Example.

```text
Latency > 1 sec

AND

Error Rate > 5%

↓

Critical Alert
```

More accurate.

Than a single metric.

---

# Alert Deduplication

One incident.

May generate.

Thousands.

Of identical alerts.

Example.

```text
500 Pods

↓

Same Database Failure

↓

500 Alerts
```

Deduplication.

Groups related alerts.

Into one incident.

---

# Alert Suppression

During planned maintenance.

Certain alerts.

Should be suppressed.

Otherwise.

Engineers receive unnecessary notifications.

---

# Alert Routing

Different alerts.

Go to different teams.

Example.

```text
Database Alerts

↓

Database Team

-------------------

Application Alerts

↓

Application Team

-------------------

Network Alerts

↓

Network Team

-------------------

Security Alerts

↓

SOC Team
```

Correct routing reduces response time.

---

# Escalation Policy

If the primary engineer.

Does not respond.

Escalate automatically.

Example.

```text
Engineer

↓

10 Minutes

↓

Team Lead

↓

Manager

↓

Incident Commander
```

Critical incidents should never remain unattended.

---

# Notification Channels

Modern organizations notify engineers through:

- PagerDuty
- Opsgenie
- Email
- Slack
- Microsoft Teams
- SMS
- Phone Calls

Critical alerts should use reliable channels that wake on-call engineers when necessary.

---

# Alert Fatigue

> **Alert Fatigue occurs when engineers receive so many alerts that they begin ignoring them.**

This is one of the biggest operational problems.

---

# Causes

Examples.

- Duplicate alerts
- Low-quality alerts
- False positives
- Poor thresholds
- No ownership
- Excessive warning alerts

Eventually.

Important alerts.

Become lost.

---

# Example

Engineer receives.

```text
1,200 Alerts

Per Day
```

Most.

Are irrelevant.

Eventually.

Critical alerts.

Are missed.

---

# Symptoms

- Ignored alerts
- Delayed acknowledgements
- Alert muting
- Burnout
- Slower incident response

---

# Reducing Alert Fatigue

Recommendations.

- Alert only on actionable conditions.
- Remove noisy alerts.
- Deduplicate incidents.
- Use appropriate severity.
- Review alerts regularly.
- Prefer symptom-based alerts over resource-only alerts.

---

# Golden Rule

Every alert should answer.

```
What action should the engineer take?
```

If no clear action exists.

The alert should be reconsidered.

---

# 37. On-Call Engineering

> **On-call engineering ensures that qualified engineers are available to respond to production incidents outside normal working hours.**

Modern internet services.

Operate.

24×7.

Incidents can occur.

At any time.

---

# On-Call Workflow

```text
Alert

↓

Pager

↓

Engineer

↓

Investigate

↓

Mitigate

↓

Resolve

↓

Postmortem
```

---

# Responsibilities

On-call engineers should:

- Acknowledge alerts
- Investigate incidents
- Restore service
- Escalate when necessary
- Document actions
- Participate in postmortems

---

# Runbooks

Every critical alert.

Should link.

To a runbook.

Example.

```
Kafka Consumer Lag

↓

Runbook

↓

Possible Causes

↓

Recovery Steps
```

Runbooks reduce recovery time.

Especially for newer engineers.

---

# Incident Commander

For major incidents.

One person.

Coordinates.

The response.

Responsibilities include:

- Communication
- Prioritization
- Coordination
- Stakeholder updates
- Decision making

This prevents confusion during high-pressure situations.

---

# War Room

Critical production incidents.

Often establish.

A dedicated communication channel.

Example.

```text
Incident

↓

Slack Channel

↓

Application Team

Database Team

Network Team

Platform Team

Security Team
```

Centralized coordination accelerates recovery.

---

# Service Ownership

Every production service.

Must have.

A clearly defined owner.

Example.

```text
Order Service

↓

Commerce Team

-------------------

Payment Service

↓

Payments Team

-------------------

Kafka

↓

Platform Team
```

Ownership eliminates confusion during incidents.

---

# Alert Quality Metrics

Organizations should measure:

- Mean Time to Acknowledge (MTTA)
- Mean Time to Detect (MTTD)
- Mean Time to Resolve (MTTR)
- False Positive Rate
- Alert Volume
- Alert Noise Ratio

Improving these metrics improves operational maturity.

---

# Common Mistakes

### Alerting on CPU Alone

High CPU does not always indicate a customer problem.

Prefer alerts tied to user impact.

---

### One Alert Per Server

Large systems may generate thousands of unnecessary alerts.

Aggregate related failures.

---

### No Escalation Policy

Critical incidents should never depend on a single individual.

---

### No Runbooks

Engineers waste valuable time searching for recovery procedures.

Document common incidents.

---

### Alerting Without Ownership

Every alert must have a responsible team.

Otherwise incidents remain unresolved.

---

# Architect's Perspective

Alerting is where observability becomes operational.

Collecting telemetry is valuable.

Visualizing dashboards is valuable.

But neither restores production systems.

Alerts initiate the human response that protects customers and the business.

Architects should design alerting systems that:

- Detect customer-impacting issues quickly.
- Minimize false positives.
- Route incidents to the correct teams.
- Escalate automatically when necessary.
- Reduce alert fatigue through thoughtful design.
- Integrate with runbooks and incident management.

The objective is not to generate more alerts.

It is to generate **fewer, higher-quality alerts that engineers trust and act upon immediately**.

---

**End of Part 5B**

The next part will cover:

- **SLI (Service Level Indicator)**
- **SLO (Service Level Objective)**
- **SLA (Service Level Agreement)**
- **Error Budgets**
- **Burn Rate Alerts**
- **Reliability Engineering**
- **Google SRE Practices**
