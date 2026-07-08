
# 24. Architecture Review Checklist

> **An architecture review validates that the system is capable of producing correct business outcomes under both normal and failure conditions.**

Architecture reviews should evaluate more than code quality.

They should verify that business correctness has been protected at every layer.

---

# Business Review

- [ ] What business problem is the system solving?
- [ ] Which business operations are mission critical?
- [ ] What business rules must never be violated?
- [ ] What is the cost of incorrect data?
- [ ] Which operations require exactly-once business outcomes?
- [ ] What regulatory requirements apply?

---

# Reliability Review

- [ ] Can duplicate requests occur?
- [ ] Are duplicate requests safely handled?
- [ ] Can messages be lost?
- [ ] Can business events be replayed?
- [ ] Can concurrent updates overwrite data?
- [ ] Can incorrect data enter the system?
- [ ] Is business history traceable?

---

# Data Integrity

- [ ] Are database constraints implemented?
- [ ] Are transactions used appropriately?
- [ ] Is data validated before persistence?
- [ ] Are foreign keys enforced?
- [ ] Is optimistic or pessimistic locking used where required?
- [ ] Can corrupted data be detected?

---

# Distributed Systems

- [ ] Are retries idempotent?
- [ ] Are timeouts configured?
- [ ] Are circuit breakers implemented?
- [ ] Are messages durable?
- [ ] Is event ordering important?
- [ ] Can events be replayed?
- [ ] Are compensating actions defined?

---

# Messaging

- [ ] Is the Outbox Pattern implemented?
- [ ] Are consumers idempotent?
- [ ] Are failed messages retained?
- [ ] Is a Dead Letter Queue configured?
- [ ] Are acknowledgements verified?
- [ ] Are poison messages handled?

---

# Observability

- [ ] Can every business transaction be traced?
- [ ] Are audit logs complete?
- [ ] Are correlation IDs propagated?
- [ ] Are business metrics monitored?
- [ ] Are reconciliation failures visible?

---

# Operations

- [ ] Are reconciliation jobs automated?
- [ ] Are backups verified?
- [ ] Have restore procedures been tested?
- [ ] Are production runbooks documented?
- [ ] Are disaster recovery exercises performed?

---

# Security

- [ ] Are business events immutable?
- [ ] Are audit logs protected?
- [ ] Are secrets securely managed?
- [ ] Is least privilege enforced?
- [ ] Are critical business actions authenticated?

---

# Architecture Review Outcome

A mature architecture should satisfy:

| Objective | Status |
|-----------|--------|
| Data Integrity Protected | ✓ |
| Duplicate Processing Prevented | ✓ |
| Business Rules Enforced | ✓ |
| Events Recoverable | ✓ |
| Operations Traceable | ✓ |
| Recovery Verified | ✓ |

---

# 25. Production Readiness Checklist

Before releasing a system into production, architects should verify that it is operationally capable of maintaining business correctness.

```markdown
## Business Rules

- [ ] Critical business rules documented
- [ ] Validation completed
- [ ] Business exceptions reviewed

## Database

- [ ] Transactions configured
- [ ] Constraints verified
- [ ] Indexes validated
- [ ] Backup completed
- [ ] Restore tested

## Messaging

- [ ] Outbox implemented
- [ ] DLQ configured
- [ ] Retry policy reviewed
- [ ] Consumer idempotency verified

## Application

- [ ] Validation implemented
- [ ] Version conflicts handled
- [ ] Timeouts configured
- [ ] Retry strategy reviewed

## Monitoring

- [ ] Business dashboards available
- [ ] Alerts configured
- [ ] Logs centralized
- [ ] Tracing enabled

## Reconciliation

- [ ] Scheduled reconciliation configured
- [ ] Audit verification enabled
- [ ] Data consistency reports available

## Security

- [ ] Secrets protected
- [ ] Audit access controlled
- [ ] Encryption verified

## Operations

- [ ] Runbooks completed
- [ ] Incident procedures documented
- [ ] Recovery exercises completed
```

---

# 26. Architecture Decision Record (ADR)

Architecture decisions should explain not only **what** was chosen but also **why**.

Future engineers should understand the reasoning behind important reliability decisions.

---

## ADR-002

### Title

Adopt Idempotency Keys for Payment Processing

---

### Status

Accepted

---

### Context

The payment platform experienced duplicate payment requests caused by:

- Mobile retries
- Network interruptions
- Client refresh
- Gateway timeouts

Duplicate payments created customer complaints and manual reconciliation.

---

### Problem

Repeated requests produced repeated business operations.

Business correctness could not be guaranteed.

---

### Decision

All payment requests must include an Idempotency Key.

Requests with an existing key return the previously computed response instead of executing the payment again.

---

### Alternatives Considered

### Alternative 1

Ignore duplicate requests.

Rejected because:

- Financial correctness could not be guaranteed.

---

### Alternative 2

Detect duplicates during reconciliation.

Rejected because:

- Customers would still experience duplicate charges.

---

### Consequences

Positive

- Safe retries
- Better customer experience
- Reduced reconciliation
- Improved financial correctness

Negative

- Additional storage
- Key expiration management
- Slight increase in latency

---

### Success Criteria

- Duplicate payment rate approaches zero.
- Safe retries become possible.
- Customer disputes decrease significantly.

---

# 27. Architecture Thinking Tips

Experienced architects think differently about Reliability than application developers.

Their focus extends beyond code to long-term business correctness.

---

## 1. Think in Business Rules

Technology implements business rules.

Business rules define correctness.

Always identify the business rule before discussing implementation.

---

## 2. Assume Every Request May Execute Twice

Retries are inevitable.

Duplicate processing should never produce duplicate business outcomes.

---

## 3. Protect Business Truth

Infrastructure supports business data.

Business data should never be sacrificed to simplify infrastructure.

---

## 4. Every Important Operation Should Be Traceable

Ask yourself:

> Can we explain exactly what happened six months from now?

If the answer is no,

Observability is insufficient.

---

## 5. Silent Failures Are More Dangerous Than Visible Failures

Downtime is immediately noticed.

Incorrect business data may remain hidden for weeks.

Prioritize mechanisms that detect silent failures.

---

## 6. Design for Recovery

Failures cannot always be prevented.

Reliable systems make recovery predictable.

Examples include:

- Event replay
- Compensation
- Reconciliation
- Restore procedures

---

## 7. Validate at Every Layer

Validation should exist in:

- APIs
- Domain layer
- Database
- Messaging
- External integrations

No single validation layer is sufficient.

---

## 8. Make Correctness Observable

Dashboards should monitor:

- Duplicate payments
- Failed business rules
- Validation errors
- Reconciliation failures
- Data inconsistencies

Not just CPU and memory.

---

## 9. Prefer Immutable History

Deleting history destroys evidence.

Appending events preserves business truth.

---

## 10. Design for Human Mistakes

People make mistakes.

Reliable architectures should minimize their impact through:

- Automation
- Peer review
- Least privilege
- Rollback procedures

---

## 11. Measure Business Correctness

Availability metrics answer:

> Is the system online?

Reliability metrics answer:

> Is the business operating correctly?

Both are required.

---

## 12. Complexity Must Be Justified

Every reliability mechanism introduces operational cost.

Architects continuously ask:

> Does this additional complexity reduce meaningful business risk?

---

## 13. Trust but Verify

Never assume:

- External systems behave correctly.
- Messages always arrive.
- Customers never retry.
- Data is always valid.

Verification is part of architecture.

---

## 14. Optimize for Long-Term Confidence

Reliable systems reduce uncertainty.

Customers trust them.

Engineers trust them.

Business leaders trust them.

Trust becomes a competitive advantage.

---

# 28. Architect's Mental Model

Experienced architects approach Reliability through a layered decision process.

```text
Business Objectives

↓

Business Rules

↓

Correct Business Outcomes

↓

Failure Analysis

↓

Architecture Decisions

↓

Reliability Mechanisms

↓

Verification

↓

Continuous Measurement

↓

Continuous Improvement
```

Rather than asking:

> "How do we prevent every failure?"

Architects ask:

- Which business failures are unacceptable?
- How can we detect them?
- How can we recover?
- How can we prove correctness?
- How can we continuously improve?

This mindset shifts architecture from infrastructure-focused thinking to business-focused engineering.

---

# Reliability Design Questions

Before approving any architecture, experienced architects typically ask:

- What business rule are we protecting?
- What happens if the client retries?
- What happens if a message is delivered twice?
- Can this workflow partially succeed?
- Can we reconstruct every business event?
- How do we detect silent corruption?
- How do we recover from inconsistent data?
- Can customers trust every business outcome?
- Is reconciliation automated?
- What assumptions are we making about external systems?

These questions guide architects toward dependable, trustworthy systems rather than simply functional software.

---

**End of Part 8**

The final part will cover:

- **29. Real Enterprise Examples**
- **30. Architecture Diagrams**
- **31. Interview Preparation**
- **32. Common Interview Mistakes**
- **33. Best Practices**
- **34. Related Concepts**
- **35. Further Reading**
- **36. Revision Notes**
- **37. Chapter Completion Checklist**
- **38. Architect's Questions**

This will complete **Chapter 2 – Reliability**.
