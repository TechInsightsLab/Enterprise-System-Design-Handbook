# 11. Threat Modeling

> **Threat Modeling is a structured process for identifying, analyzing, prioritizing, and mitigating security threats before a system is built or modified.**

Threat modeling answers a simple question:

> **"What could go wrong?"**

Instead of waiting for attackers to discover vulnerabilities,

architects identify them during design.

---

# Why Threat Modeling?

Imagine designing an online payment system.

Without threat modeling.

You ask:

> Does the application work?

With threat modeling.

You ask:

- Can someone steal money?
- Can someone impersonate another user?
- Can someone modify transactions?
- Can someone bypass authentication?
- Can someone overload the API?
- Can an insider misuse privileges?

The second approach produces far more secure systems.

---

# Business Benefits

Threat modeling helps organizations:

- Prevent security incidents
- Reduce remediation costs
- Meet compliance requirements
- Protect customer trust
- Improve architecture quality
- Prioritize security investments

Finding security flaws during design is significantly cheaper than fixing them after deployment.

---

# Threat Modeling Process

A typical process is:

```text
Understand System

↓

Identify Assets

↓

Identify Threats

↓

Assess Risk

↓

Design Mitigations

↓

Validate Controls
```

This process should be repeated whenever significant architectural changes occur.

---

# When Should Threat Modeling Be Performed?

Threat modeling should occur:

- During architecture design
- Before major feature releases
- Before exposing public APIs
- Before cloud migration
- Before integrating third-party systems
- During major infrastructure changes

Threat modeling is not a one-time activity.

It evolves with the system.

---

# Who Participates?

Threat modeling is a collaborative exercise.

Participants include:

- Solution Architects
- Security Architects
- Developers
- DevSecOps Engineers
- Infrastructure Engineers
- Product Owners
- Business Stakeholders

Different perspectives reveal different risks.

---

# Example

Consider an Online Banking System.

```text
Customer

↓

Web Application

↓

API Gateway

↓

Account Service

↓

Payment Service

↓

Database
```

Threat modeling examines each component.

And every interaction.

---

# Identify Assets

> **Assets are anything valuable that must be protected.**

Examples:

- Customer Data
- Payment Information
- Personal Information
- Authentication Tokens
- Encryption Keys
- Source Code
- Databases
- Business Logic
- Intellectual Property

---

# Asset Classification

Not every asset has equal value.

Example.

```text
Public Product Catalog

↓

Medium Value

------------------

Customer Password Database

↓

Critical Asset

------------------

Private Encryption Keys

↓

Highest Value
```

Higher-value assets require stronger protection.

---

# Threat Actors

Threats originate from many sources.

Examples:

```text
Threat Actors

├── Cyber Criminals
├── Nation States
├── Hacktivists
├── Competitors
├── Insider Threats
├── Malicious Employees
├── Curious Employees
├── Automated Bots
└── Accidental Users
```

Not every attack is intentional.

Misconfiguration is one of the largest causes of security incidents.

---

# Insider Threats

Insiders may already possess:

- Network access
- Credentials
- System knowledge

Threats may be:

- Malicious
- Negligent
- Compromised accounts

Architectures should not assume insiders are always trustworthy.

---

# External Threats

Examples:

- Credential stuffing
- API abuse
- SQL Injection
- DDoS
- Phishing
- Malware
- Supply-chain attacks

---

# Attack Surface

> **The attack surface is the total collection of entry points through which an attacker can interact with a system.**

Examples include:

- APIs
- Login pages
- Admin portals
- Mobile applications
- Open ports
- VPN gateways
- Kubernetes API Server
- Databases
- Third-party integrations

---

# Example Attack Surface

```text
Internet

↓

Web UI

↓

API Gateway

↓

Authentication

↓

Microservices

↓

Database
```

Every component increases the attack surface.

---

# Reduce Attack Surface

Architects should minimize exposed components.

Examples:

Instead of exposing.

```text
25 APIs
```

Expose.

```
8 APIs
```

Disable:

- Unused endpoints
- Unused ports
- Default accounts
- Debug interfaces
- Test environments

---

# Trust Boundaries

> **A Trust Boundary is a point where the level of trust changes and additional security controls are required.**

Every trust boundary should trigger verification.

---

# Example

```text
Internet

↓

Firewall

================

API Gateway

↓

Internal Network
```

Firewall represents.

One trust boundary.

---

Another.

```text
Customer

↓

API Gateway

================

Microservices
```

Authentication occurs.

At the boundary.

---

Another.

```text
Application

↓

Database
```

Database credentials.

Encryption.

Authorization.

Protect the boundary.

---

# Common Trust Boundaries

- Internet → Organization
- User → API
- API → Microservice
- Microservice → Database
- Cloud → On-premises
- Partner → Enterprise
- Kubernetes Namespace → Namespace

Every boundary deserves explicit protection.

---

# Data Flow Diagrams (DFD)

Threat modeling commonly begins with a Data Flow Diagram.

Example.

```text
Customer

↓

Browser

↓

API Gateway

↓

Order Service

↓

Database
```

Data movement.

Becomes visible.

Security analysis becomes easier.

---

# Security Questions

For every component ask:

- What data enters?
- What data leaves?
- Who can access it?
- What happens if it fails?
- What happens if compromised?
- What logs are generated?
- How is identity verified?

These questions uncover hidden risks.

---

# 12. STRIDE Threat Model

> **STRIDE is Microsoft's widely adopted threat modeling framework for categorizing security threats.**

STRIDE stands for:

```text
S

Spoofing

T

Tampering

R

Repudiation

I

Information Disclosure

D

Denial of Service

E

Elevation of Privilege
```

Every secure architecture should evaluate all six categories.

---

# STRIDE Overview

| Threat | Security Property Affected |
|----------|---------------------------|
| Spoofing | Authentication |
| Tampering | Integrity |
| Repudiation | Accountability |
| Information Disclosure | Confidentiality |
| Denial of Service | Availability |
| Elevation of Privilege | Authorization |

---

# Spoofing

> **Pretending to be someone or something else.**

Examples.

- Stolen password
- Fake JWT
- Session hijacking
- Identity impersonation

---

# Example

Attacker logs in.

Using.

Another customer's credentials.

Identity.

Spoofed.

---

# Mitigations

- MFA
- Strong passwords
- OAuth
- OIDC
- mTLS
- Device verification

---

# Tampering

> **Unauthorized modification of data or code.**

Examples.

- Change payment amount
- Modify API request
- Alter configuration
- Malware modifying binaries

---

# Example

Original payment.

```
₹500
```

Modified.

```
₹50,000
```

Integrity violated.

---

# Mitigations

- Digital signatures
- Hashing
- TLS
- Database constraints
- Immutable logs

---

# Repudiation

> **A user denies performing an action because there is insufficient evidence to prove it.**

Example.

Employee claims.

```
"I never deleted that customer."
```

No audit logs.

Cannot prove.

---

# Mitigations

- Audit logging
- Digital signatures
- Immutable event logs
- Correlation IDs
- Time synchronization

---

# Information Disclosure

> **Sensitive information becomes accessible to unauthorized parties.**

Examples.

- Password leak
- Credit card exposure
- Public cloud bucket
- Debug logs exposing secrets

---

# Mitigations

- Encryption
- Authorization
- Data masking
- Tokenization
- Secure logging

---

# Denial of Service (DoS)

> **Preventing legitimate users from accessing the system.**

Examples.

- API flooding
- Bot traffic
- Resource exhaustion
- Expensive database queries

---

# Mitigations

- Rate limiting
- Autoscaling
- CDN
- WAF
- DDoS protection
- Circuit breakers

---

# Elevation of Privilege

> **A user gains permissions beyond those originally granted.**

Example.

Customer.

Becomes.

Administrator.

Without authorization.

---

# Mitigations

- RBAC
- ABAC
- Least privilege
- Authorization checks
- Security testing

---

# STRIDE Example

Online Shopping System.

```text
Customer

↓

API Gateway

↓

Order Service

↓

Database
```

Threats.

| Component | STRIDE Example |
|------------|----------------|
| Login API | Spoofing |
| Order API | Tampering |
| Audit Logs | Repudiation |
| Customer Data | Information Disclosure |
| Public API | DoS |
| Admin APIs | Elevation of Privilege |

---

# Risk Assessment

Not every threat deserves equal attention.

Architects evaluate:

```text
Likelihood

×

Impact

=

Risk
```

---

# Example

| Threat | Likelihood | Impact | Priority |
|----------|------------|---------|----------|
| SQL Injection | High | Critical | Very High |
| Data Leak | Medium | Critical | Very High |
| Printer Failure | Low | Low | Low |

---

# Risk Matrix

```text
              Impact

        Low   Med   High

Low      L     L      M

Likelihood

Med      L     M      H

High     M     H      Critical
```

Prioritize.

High-impact.

High-likelihood.

Threats.

---

# Security by Design

Security should be designed.

Not bolted on.

Later.

Examples.

Instead of adding:

Encryption.

At deployment.

Design encryption.

During architecture.

---

# Security Design Questions

Architects should always ask:

- Where is sensitive data stored?
- Who owns the data?
- Where are trust boundaries?
- What is the blast radius?
- What happens if this service is compromised?
- Can attackers move laterally?
- Can permissions be abused?
- Can this API be automated by bots?
- How is every important action audited?

These questions become part of every architecture review.

---

# Common Mistakes

### Threat Modeling Only Before Release

Threat modeling should evolve as the architecture evolves.

---

### Ignoring Internal Threats

Many incidents originate from compromised internal systems or privileged accounts.

Design accordingly.

---

### Protecting Everything Equally

Security investment should be proportional to asset value and business risk.

---

### No Trust Boundaries

Assuming every internal service is trusted violates Zero Trust principles.

---

### Focusing Only on Technology

Many security incidents involve:

- People
- Processes
- Misconfiguration

Threat modeling should consider all three.

---

# Architect's Perspective

Threat modeling is one of the highest-leverage activities a Solution Architect can perform.

Rather than asking:

> **"How do we stop attackers?"**

Ask:

- What are our most valuable assets?
- Who might attack them?
- How would they attack?
- What is the business impact?
- Which controls reduce the greatest risk?

Architects who consistently perform threat modeling build systems that are secure **by design**, rather than relying on reactive fixes after incidents occur.

---

**End of Part 2B**

The next part will cover:

- **Identity Management**
- **Authentication**
- **Authorization**
- **RBAC**
- **ABAC**
- **PBAC**
- **ACL**
- **Multi-Factor Authentication (MFA)**
- **Single Sign-On (SSO)**
- **Identity Federation**
