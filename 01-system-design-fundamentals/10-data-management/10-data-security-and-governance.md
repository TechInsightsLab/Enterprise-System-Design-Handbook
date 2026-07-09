
# 93. Data Security

> **Data security is the practice of protecting data from unauthorized access, modification, disclosure, destruction, and theft throughout its entire lifecycle.**

For most organizations,

the database is the most valuable asset.

Applications can be rebuilt.

Infrastructure can be replaced.

Lost or stolen customer data can permanently damage a business.

---

# Why Data Security Matters

Imagine an online banking platform.

Attackers gain access to:

- Customer accounts
- Credit card numbers
- Transaction history
- Personal information

Consequences include:

- Financial loss
- Regulatory penalties
- Lawsuits
- Brand damage
- Customer churn

Security is therefore a business requirement,

not merely a technical feature.

---

# CIA Triad

Almost every security framework is built around three principles.

```text
Confidentiality

↓

Integrity

↓

Availability
```

Known as:

```
CIA Triad
```

---

# Confidentiality

Only authorized users should access data.

Example:

Only HR personnel can view employee salaries.

---

# Integrity

Data must remain accurate.

Unauthorized modification must be prevented.

Example:

Customers cannot modify:

```
Payment Amount
```

---

# Availability

Authorized users should access data whenever needed.

Example:

Hospital systems must remain accessible 24×7.

---

# Security Layers

Enterprise data security operates across multiple layers.

```text
Users

↓

Authentication

↓

Authorization

↓

Application

↓

Database

↓

Storage

↓

Backup
```

Every layer must be protected.

---

# Defense in Depth

Never rely on a single security control.

Instead,

implement multiple independent layers.

```text
Firewall

↓

API Gateway

↓

Authentication

↓

Authorization

↓

Encryption

↓

Audit Logging

↓

Monitoring
```

If one layer fails,

others continue protecting the system.

---

# 94. Encryption

> **Encryption converts readable data (plaintext) into unreadable data (ciphertext) using cryptographic algorithms.**

Without the correct key,

encrypted data is useless.

---

# Example

Before encryption:

```
4111 1111 1111 1111
```

After encryption:

```
A9F73C21B81D9E...
```

Even if stolen,

attackers cannot read it without the key.

---

# Encryption Goals

Protect against:

- Data theft
- Insider attacks
- Lost disks
- Backup compromise
- Cloud storage exposure

---

# Encryption Types

```text
Encryption

├── At Rest

└── In Transit
```

Both are essential.

---

# 95. Encryption at Rest

> **Encryption at Rest protects stored data.**

Examples:

- Database files
- SSDs
- Object storage
- Backup files
- Snapshots

---

# Architecture

```text
Application

↓

Encrypted Database

↓

Encrypted Disk
```

Even if the storage device is stolen,

data remains protected.

---

# Examples

Cloud providers offer encryption for:

- AWS RDS
- Azure SQL Database
- Google Cloud SQL
- Amazon S3
- Azure Blob Storage

Most managed database services support encryption at rest with minimal configuration.

---

# Typical Algorithms

Common algorithms include:

- AES-256
- AES-128

AES-256 is widely used for enterprise storage encryption.

---

# Benefits

- Protects stolen disks
- Secures backups
- Meets compliance requirements
- Minimal application changes

---

# 96. Encryption in Transit

> **Encryption in Transit protects data while it moves across networks.**

Without transport encryption,

attackers may intercept traffic.

---

# Example

```text
Browser

↓

HTTPS

↓

Application

↓

TLS

↓

Database
```

Every network connection should be encrypted.

---

# Typical Technologies

- TLS
- HTTPS
- SSH
- IPSec
- mTLS

---

# Why It Matters

Without TLS:

Anyone on the network may read:

- Passwords
- API tokens
- Customer data
- Payment details

---

# Enterprise Rule

Encrypt:

- Client → API
- API → Service
- Service → Database
- Database → Replica
- Backup → Storage

---

# 97. Key Management

> **Encryption is only as secure as the protection of its keys.**

One of the most common mistakes:

```text
Encrypted Data

↓

Encryption Key

↓

Same Server
```

If attackers obtain both,

encryption provides little protection.

---

# Secure Architecture

```text
Application

↓

Key Management System

↓

Encryption Keys

↓

Database
```

Keys stored separately.

---

# Enterprise Key Management

Popular services:

| Cloud | Service |
|--------|----------|
| AWS | KMS |
| Azure | Key Vault |
| Google Cloud | Cloud KMS |
| HashiCorp | Vault |

---

# Key Rotation

Keys should not live forever.

Regular rotation reduces exposure.

Example:

```text
Key V1

↓

Key V2

↓

Key V3
```

Applications migrate gradually.

---

# Benefits

- Reduced compromise impact
- Compliance
- Easier recovery
- Better governance

---

# 98. Secrets Management

Secrets include:

- Database passwords
- API keys
- JWT signing keys
- OAuth secrets
- Certificates
- Encryption keys

---

# Bad Practice

```java
password="admin123"
```

Stored in source code.

---

# Better Practice

```text
Application

↓

Secrets Manager

↓

Runtime Injection
```

Secrets never committed to Git.

---

# Popular Tools

- HashiCorp Vault
- AWS Secrets Manager
- Azure Key Vault
- Google Secret Manager
- Kubernetes Secrets (preferably backed by external secret stores for highly sensitive data)

---

# Best Practices

- Never hardcode secrets.
- Rotate secrets regularly.
- Grant least privilege.
- Audit secret access.
- Separate secrets by environment.

---

# 99. Data Masking

> **Data Masking hides sensitive information while preserving the overall format and usefulness of the data.**

Example:

Original:

```
9876543210
```

Masked:

```
98******10
```

---

# Why?

Developers rarely need:

Real customer data.

Masking protects privacy.

---

# Use Cases

- Testing
- Development
- Customer Support
- Demonstrations
- Training

---

# Dynamic Data Masking

Different users see different values.

Administrator:

```
9876543210
```

Support Engineer:

```
98******10
```

Access depends on authorization.

---

# 100. Tokenization

Tokenization replaces sensitive data with:

Random tokens.

---

# Example

Original Card:

```
4111111111111111
```

Stored Token:

```
TKN_9F73AB21
```

Actual card stored separately in a secure vault.

---

# Difference

Encryption:

Can be decrypted using a key.

---

Tokenization:

Original value replaced.

Lookup required.

---

# Typical Use Cases

- Payment systems
- Healthcare
- Personally Identifiable Information (PII)
- Government IDs

---

# 101. Data Governance

> **Data Governance defines the policies, ownership, standards, and processes that ensure enterprise data is trustworthy and properly managed.**

Technology alone is insufficient.

Governance defines:

- Who owns data
- Who can access it
- How long it is retained
- How quality is maintained

---

# Governance Components

```text
Ownership

↓

Quality

↓

Security

↓

Compliance

↓

Lifecycle
```

---

# Data Steward

Many enterprises assign:

```
Data Steward
```

Responsible for:

- Data quality
- Metadata
- Standards
- Definitions

---

# Data Owner

Responsible for:

Business decisions regarding data.

Not necessarily technical implementation.

---

# Data Custodian

Responsible for:

Technical protection.

Examples:

DBAs.

Cloud Engineers.

Infrastructure Teams.

---

# Governance Principles

- Single Source of Truth
- Data Quality
- Ownership
- Classification
- Compliance
- Auditability

---

# 102. Compliance

Different industries require different regulations.

Architects must understand applicable compliance requirements.

---

# GDPR

**General Data Protection Regulation**

Applies to organizations handling personal data of individuals in the European Union.

Core principles include:

- Data minimization
- Right to access
- Right to erasure
- Consent management
- Privacy by design

---

# HIPAA

Protects healthcare information in the United States.

Focus:

- Patient privacy
- Medical records
- Access control
- Audit logging

---

# PCI DSS

Applies to organizations processing payment card data.

Requirements include:

- Encryption
- Network segmentation
- Access control
- Vulnerability management
- Security monitoring

---

# SOC 2

Framework focused on organizational controls for:

- Security
- Availability
- Processing Integrity
- Confidentiality
- Privacy

Widely requested for SaaS providers.

---

# Compliance Comparison

| Standard | Primary Focus |
|-----------|---------------|
| GDPR | Personal Data Privacy |
| HIPAA | Healthcare Information |
| PCI DSS | Payment Card Data |
| SOC 2 | Organizational Security Controls |

---

# 103. Audit Logging

Every sensitive operation should be auditable.

Examples:

- Login
- Logout
- Password Change
- Payment Approval
- Data Export
- Permission Change

---

# Example

```text
User

↓

Updated Customer

↓

Time

↓

IP Address

↓

Previous Value

↓

New Value
```

Complete traceability.

---

# Best Practices

Audit logs should be:

- Immutable
- Timestamped
- Secure
- Searchable
- Retained according to policy

Audit logs should not be modifiable by ordinary application users.

---

# 104. Data Classification

Not all data requires the same level of protection.

---

# Example

```text
Public

↓

Internal

↓

Confidential

↓

Restricted
```

---

# Examples

Public:

- Product catalog
- Public documentation

---

Internal:

- Employee directory
- Internal dashboards

---

Confidential:

- Customer records
- Contracts
- Financial reports

---

Restricted:

- Password hashes
- Encryption keys
- Government IDs
- Payment card data

---

# Common Security Mistakes

### Hardcoding Secrets

Never store credentials in source code.

---

### Using Production Data in Testing

Always mask or generate synthetic data.

---

### Encrypting Only the Database

Data must also be protected:

- In transit
- In backups
- In logs

---

### Excessive Privileges

Every service should receive only the permissions it actually needs.

Principle:

```
Least Privilege
```

---

### Ignoring Key Rotation

Long-lived keys increase security risk.

Rotate regularly.

---

# Enterprise Security Architecture

```text
Users

↓

Identity Provider

↓

Authentication

↓

Authorization

↓

Application

↓

Secrets Manager

↓

Encrypted Database

↓

Encrypted Backup

↓

Archive

↓

Audit Logs

↓

Monitoring
```

Security spans the entire lifecycle.

---

# Architect's Perspective

Data security is not achieved through encryption alone.

It is achieved through **layered controls** that work together:

- Identity
- Authentication
- Authorization
- Encryption
- Key Management
- Secrets Management
- Governance
- Monitoring
- Auditing
- Compliance

The strongest encryption algorithm is ineffective if:

- Keys are exposed.
- Secrets are hardcoded.
- Backups are unencrypted.
- Excessive permissions are granted.

Enterprise architects therefore treat **security as an architectural quality attribute**, embedded into every layer of the platform rather than added as a final step.

> **Protect the data, protect the keys, protect the backups, and continuously verify who can access what.**

---

**End of Part 10**

The next part will cover:

- **Cloud Database Services (AWS, Azure, GCP)**
- **Database Selection Matrix**
- **Enterprise Case Studies (Amazon, Netflix, Uber, Google, LinkedIn)**
- **Production Incidents**
- **Architecture Review Checklist**
- **ADR**
- **Interview Questions**
- **Revision Cheat Sheet**
- **Chapter Summary**
