
# 1. CIA Triad

> **The CIA Triad is the foundation of Information Security and serves as the primary objective for designing secure systems.**

Every security control.

Ultimately protects.

One or more of:

```text
Confidentiality

↓

Integrity

↓

Availability
```

Nearly every security decision made by a Solution Architect can be mapped back to the CIA Triad.

---

# Why the CIA Triad Exists

Imagine an online banking system.

Customer balance:

```
₹1,50,000
```

Security must ensure:

- Nobody else can view it.
- Nobody can modify it illegally.
- Customer can access it whenever needed.

These correspond to:

```text
Confidentiality

Integrity

Availability
```

---

# CIA Overview

| Principle | Question |
|------------|----------|
| Confidentiality | Who can see the data? |
| Integrity | Can the data be trusted? |
| Availability | Can authorized users access the system? |

---

# Confidentiality

> **Confidentiality ensures that information is accessible only to authorized users, systems, or processes.**

Unauthorized users should never access:

- Personal Information
- Financial Data
- Medical Records
- Passwords
- Business Secrets
- Source Code

---

# Business Example

Hospital System.

Patient records.

Must be visible only to:

- Doctors
- Authorized Nurses
- Patient

Not.

Every employee.

---

# Techniques

Confidentiality is achieved through:

- Authentication
- Authorization
- Encryption
- Tokenization
- Data Masking
- Network Segmentation
- Access Control

---

# Real Example

Customer Database.

```text
Customer

↓

Login

↓

JWT

↓

Authorization

↓

Customer Data
```

Unauthorized request.

Rejected.

---

# Confidentiality Threats

Examples.

- Stolen passwords
- Insider attacks
- Misconfigured cloud storage
- SQL Injection
- Credential stuffing
- API authorization failures
- Data leaks

---

# Integrity

> **Integrity ensures that data remains accurate, complete, and unaltered except by authorized changes.**

Users should trust.

That data.

Has not been modified.

Incorrectly.

---

# Business Example

Bank Transfer.

Original.

```
₹10,000
```

Attacker changes.

```
₹1,000,000
```

Integrity.

Broken.

---

# Integrity Techniques

Protect integrity using:

- Digital Signatures
- Hashing
- Checksums
- Database Constraints
- Optimistic Locking
- Audit Logs
- Transactions

---

# Example

Software Download.

Vendor publishes.

SHA-256 Hash.

Customer downloads.

Computes hash.

Both match.

File trusted.

---

# Threats to Integrity

Examples.

- Unauthorized updates
- Malware
- Data corruption
- Replay attacks
- Concurrent write conflicts
- SQL Injection

---

# Availability

> **Availability ensures that systems and data remain accessible to authorized users whenever required.**

Even perfectly secure systems.

Fail.

If unavailable.

---

# Business Example

Payment Gateway.

Unavailable.

For.

30 minutes.

Business loses:

- Revenue
- Customer trust
- Transactions

---

# Availability Techniques

- Load Balancing
- High Availability
- Failover
- Replication
- Autoscaling
- Disaster Recovery
- DDoS Protection
- Health Checks

---

# Availability Threats

- Hardware failure
- DDoS attacks
- Network outage
- Database failure
- Cloud region outage
- Ransomware

---

# CIA Trade-offs

Architects frequently balance these goals.

Example.

Encrypting every request.

Improves.

Confidentiality.

May increase.

Latency.

Another example.

Highly available.

Public APIs.

Need stronger.

Rate limiting.

To preserve availability.

---

# Example Mapping

| Scenario | CIA Principle |
|----------|---------------|
| Encrypt customer data | Confidentiality |
| Verify file checksum | Integrity |
| Multi-region deployment | Availability |
| RBAC | Confidentiality |
| Digital Signature | Integrity |
| Load Balancer | Availability |

---

# 2. AAA Model

> **AAA stands for Authentication, Authorization, and Accounting. It defines how users are identified, what they can do, and how their actions are recorded.**

The AAA model is widely used in:

- Enterprise Applications
- Cloud Platforms
- VPNs
- Network Devices
- IAM Systems

---

# AAA Overview

```text
Authentication

↓

Authorization

↓

Accounting
```

Each stage answers a different question.

---

# Authentication

Question.

```
Who are you?
```

Verify identity.

Examples.

- Password
- OTP
- Face ID
- Certificate
- OAuth Login

---

# Authorization

Question.

```
What are you allowed to do?
```

Examples.

Customer.

Can.

```
View Orders
```

Admin.

Can.

```
Delete Users
```

---

# Accounting

Question.

```
What did you do?
```

Record.

- Login
- Logout
- API Calls
- Data Changes
- Failed Attempts

---

# Example

```text
Login

↓

Authenticated

↓

Authorized

↓

API Access

↓

Audit Log
```

---

# Why Accounting Matters

Suppose.

Sensitive customer data.

Was deleted.

Who did it?

Without audit logs.

Impossible to know.

Accounting enables:

- Auditing
- Compliance
- Forensics
- Incident investigation

---

# Example Audit Record

```text
Time

2026-07-09

User

John

Action

DELETE_ORDER

Order

500

IP

10.1.2.5
```

---

# CIA vs AAA

| CIA | AAA |
|------|------|
| Security goals | Security process |
| Protects information | Controls user access |
| Strategic | Operational |

Both complement each other.

---

# 3. Security Design Principles

Security is not achieved.

Through one technology.

It is achieved.

By consistently applying proven principles.

---

# Core Principles

```text
Security Principles

├── Least Privilege
├── Defense in Depth
├── Zero Trust
├── Fail Secure
├── Secure by Default
├── Separation of Duties
├── Complete Mediation
├── Economy of Mechanism
├── Open Design
└── Minimize Attack Surface
```

These principles appear repeatedly throughout enterprise architecture.

---

# 4. Principle of Least Privilege (PoLP)

> **Every user, service, or application should receive only the minimum permissions required to perform its task.**

Never grant.

More access.

Than necessary.

---

# Example

Bad.

```text
Developer

↓

Database Admin
```

---

Better.

```text
Developer

↓

Read Only

Development Database
```

---

# Cloud Example

Application.

Needs.

Read.

From S3 bucket.

Do not grant.

```
AdministratorAccess
```

Grant only:

```
s3:GetObject
```

---

# Benefits

- Reduced blast radius
- Better compliance
- Fewer accidental changes
- Lower insider risk

---

# Common Violations

- Shared admin accounts
- Root account usage
- Wildcard IAM policies
- Long-lived credentials

---

# 5. Defense in Depth

> **Defense in Depth is the practice of protecting a system using multiple independent security layers so that if one layer fails, others continue to provide protection.**

Never rely on.

One control.

---

# Example

Banking Application.

```text
Firewall

↓

WAF

↓

API Gateway

↓

Authentication

↓

Authorization

↓

Input Validation

↓

Encryption

↓

Database Security
```

If one layer fails.

Others remain.

---

# Castle Analogy

```text
Moat

↓

Wall

↓

Gate

↓

Guard

↓

Vault
```

Attacker.

Must defeat.

Multiple defenses.

---

# Layers

Typical enterprise layers.

- Network
- Identity
- Application
- API
- Data
- Infrastructure
- Monitoring

---

# Benefits

- Reduced risk
- Better resilience
- Multiple detection points
- Limits attack progression

---

# 6. Zero Trust Architecture

> **Zero Trust is a security model based on the principle: "Never Trust, Always Verify."**

Traditional model.

Trusted.

Internal network.

---

Modern reality.

Attackers.

Already inside.

Network.

---

# Traditional Model

```text
Internet

↓

Firewall

↓

Trusted Network
```

Once inside.

Everything trusted.

Dangerous.

---

# Zero Trust Model

```text
Every Request

↓

Authenticate

↓

Authorize

↓

Validate Device

↓

Evaluate Risk

↓

Allow
```

Every request.

Regardless of origin.

Must be verified.

---

# Zero Trust Principles

- Verify explicitly
- Use least privilege
- Assume breach
- Continuously evaluate trust
- Protect every resource

---

# Enterprise Example

Employee.

Inside office.

Requests HR API.

Still must provide:

- Identity
- Device posture
- MFA (when required)
- Authorization

Being "inside the network" is no longer sufficient.

---

# 7. Fail Secure

> **When a system encounters an error, it should default to a secure state rather than an insecure one.**

Example.

Authentication service unavailable.

Bad.

```
Allow Login
```

Good.

```
Deny Login
```

Until authentication is restored.

---

# Examples

Permission check fails.

Result should be:

```
Access Denied
```

Not.

```
Access Granted
```

---

# 8. Secure by Default

Systems should be secure.

Immediately after installation.

Without requiring extensive configuration.

---

# Good Examples

- Default TLS enabled
- Strong password policies
- MFA available
- Least privilege roles
- Logging enabled
- Secure headers configured

---

# Bad Examples

- Default admin/admin credentials
- Open databases
- Public storage buckets
- Debug mode enabled in production

---

# 9. Separation of Duties

Critical actions should require.

Different people.

Or independent approvals.

---

# Banking Example

Employee A.

Creates payment.

Employee B.

Approves payment.

No single employee controls the full process.

---

# Benefits

- Reduces fraud
- Prevents abuse
- Improves compliance

---

# 10. Minimize Attack Surface

Every exposed component.

Can be attacked.

Reduce unnecessary exposure.

---

Instead of exposing.

```text
20 APIs
```

Expose.

```
5
```

Disable:

- Unused ports
- Unused services
- Unnecessary endpoints
- Default accounts

Less exposure.

Lower risk.

---

# Security vs Usability

Security introduces friction.

Example.

MFA improves security.

But adds login steps.

Architects balance:

- Security
- User experience
- Cost
- Compliance
- Operational complexity

The correct balance depends on business risk.

---

# Common Mistakes

### Trusting Internal Networks

Modern attacks frequently originate from compromised internal systems.

Adopt Zero Trust principles.

---

### Granting Administrator Access

Convenient today.

Expensive tomorrow.

Use least privilege.

---

### Single Security Layer

One firewall.

Is not sufficient.

Use defense in depth.

---

### Failing Open

When security components fail,

prefer secure failure unless business requirements explicitly justify another approach.

---

# Architect's Perspective

Security principles are **timeless**.

Technologies evolve:

- Firewalls
- Clouds
- Kubernetes
- AI

But these principles remain constant.

Every architecture decision should be evaluated against questions such as:

- Does this reduce privilege?
- Are there multiple independent defenses?
- Does the system fail securely?
- Is trust continuously verified?
- Can this increase the attack surface?

Architects who consistently apply these principles build systems that remain secure even as technology changes.

---

**End of Part 2A**

The next part will cover:

- **Threat Modeling**
- **STRIDE**
- **Attack Surface**
- **Trust Boundaries**
- **Threat Actors**
- **Risk Assessment**
- **Security by Design**
