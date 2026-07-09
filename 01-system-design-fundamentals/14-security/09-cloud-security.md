
# 66. Cloud Security

> **Cloud Security is the practice of protecting cloud infrastructure, applications, data, identities, and workloads using architectural, operational, and security controls.**

Moving to the cloud does **not** eliminate security responsibilities.

It changes them.

Architects must understand:

- What the cloud provider secures.
- What the customer must secure.

---

# Why Cloud Security Matters

Consider an organization migrating its banking application.

From.

```text
On-Premises

↓

Cloud
```

Many assume.

"The cloud provider handles security."

This is incorrect.

Cloud providers secure.

The cloud infrastructure.

Customers secure.

Everything they build.

---

# Cloud Security Objectives

Cloud security protects:

```text
Identities

↓

Applications

↓

Data

↓

Infrastructure

↓

Containers

↓

Networks

↓

Secrets
```

Every layer requires dedicated controls.

---

# Security Challenges in Cloud

Modern cloud environments introduce new risks.

Examples.

- Public storage buckets
- Overly permissive IAM policies
- Exposed Kubernetes clusters
- Secret leakage
- Container vulnerabilities
- Supply chain attacks
- Misconfigured networks

Misconfiguration remains one of the leading causes of cloud security incidents.

---

# 67. Shared Responsibility Model

> **The Shared Responsibility Model defines which security responsibilities belong to the cloud provider and which belong to the customer.**

This is one of the most important cloud security concepts.

---

# Responsibility Split

```text
Cloud Provider

↓

Physical Data Centers

Networking

Hardware

Hypervisors

Core Managed Services

-------------------------

Customer

↓

Identity

Applications

Operating Systems (where applicable)

Data

Configurations

Secrets

Access Policies
```

---

# Example

Running a Virtual Machine.

Cloud provider secures:

- Physical server
- Storage hardware
- Hypervisor
- Networking infrastructure

Customer secures:

- Operating System
- SSH Access
- Firewall Rules
- Installed Software
- User Accounts

---

# Managed Services

Managed databases reduce operational work.

But customers still manage:

- Database users
- Access policies
- Encryption settings
- Backup policies
- Data classification

Managed does not mean fully managed from a security perspective.

---

# Architect's Rule

Always ask.

```
Who owns this responsibility?
```

Never assume.

The provider secures everything.

---

# 68. Cloud Identity and Access Management (IAM)

Cloud IAM controls.

Who can access.

Which cloud resources.

---

# IAM Architecture

```text
User

↓

IAM

↓

Roles

↓

Permissions

↓

Cloud Resources
```

---

# IAM Components

```text
IAM

├── Users
├── Groups
├── Roles
├── Policies
├── Service Accounts
└── Federated Identities
```

---

# Example

Application.

Needs.

Read.

Object Storage.

Grant.

```
Read Objects
```

Only.

Never grant.

```
Administrator
```

---

# IAM Best Practices

- Least privilege
- Short-lived credentials
- MFA
- Role-based access
- No shared accounts
- Regular access reviews

---

# Temporary Credentials

Prefer.

Temporary credentials.

Instead of.

Long-lived access keys.

Modern cloud platforms provide mechanisms for workloads to obtain temporary credentials automatically.

---

# Service Accounts

Applications should authenticate.

Using.

Dedicated service identities.

Not personal user accounts.

Example.

```text
Order Service

↓

Service Account

↓

Cloud Storage
```

---

# Root Account

Cloud root accounts.

Should be:

- Rarely used
- Protected with MFA
- Monitored
- Reserved for emergencies

Never use the root account for daily operations.

---

# 69. Secure Storage

Cloud storage often contains:

- Customer data
- Backups
- Images
- Logs
- Documents

Storage security is critical.

---

# Storage Types

Examples.

```text
Object Storage

↓

Block Storage

↓

File Storage

↓

Database Storage
```

Each requires different security controls.

---

# Encryption at Rest

Sensitive data.

Should be encrypted.

Before storage.

Or.

Automatically by the storage service.

---

# Encryption in Transit

Applications should access storage.

Using.

TLS.

Not plain HTTP.

---

# Access Policies

Storage should not be publicly accessible.

Unless explicitly intended.

Examples.

Public.

```
Website Images
```

Private.

```
Customer Documents
```

---

# Object Storage Misconfiguration

One of the most common cloud incidents.

Public bucket.

Contains.

Customer information.

Result.

Massive data exposure.

Always review bucket policies.

---

# Data Classification

Not every object requires identical protection.

Example.

```text
Public Images

↓

Public Read

-------------------

Customer Documents

↓

Private

Encrypted

-------------------

Financial Records

↓

Private

Encrypted

Access Logged
```

Security should align with data sensitivity.

---

# 70. Secrets Management

Applications require.

Secrets.

Examples.

- Database Passwords
- API Keys
- JWT Signing Keys
- Certificates
- OAuth Secrets

---

# Never Store Secrets

Never commit.

Secrets.

Into:

- Git repositories
- Docker images
- Source code
- Configuration files

---

# Secure Architecture

```text
Application

↓

Secrets Manager

↓

Runtime Secret

↓

Memory
```

Secrets are retrieved securely at runtime.

---

# Secret Rotation

Secrets should be rotated:

- Periodically
- After compromise
- During employee offboarding
- According to compliance requirements

Automation is preferred.

---

# Secret Access

Every secret should have:

- Owner
- Audit log
- Expiration policy
- Least privilege access

---

# 71. Container Security

Containers package.

Applications.

Together with dependencies.

Containers improve portability.

But require dedicated security.

---

# Container Security Lifecycle

```text
Build

↓

Scan

↓

Sign

↓

Deploy

↓

Monitor

↓

Patch
```

Security applies at every stage.

---

# Risks

Examples.

- Vulnerable base images
- Embedded secrets
- Privileged containers
- Outdated packages
- Untrusted registries

---

# Base Images

Prefer.

Minimal images.

Example.

Instead of.

```
Full Linux Distribution
```

Use.

```
Minimal Runtime Image
```

Smaller images reduce attack surface.

---

# Container Privileges

Containers should not run.

As.

Root.

Whenever possible.

Use non-root users.

---

# Immutable Containers

Containers should be treated as immutable.

Avoid logging into running containers to make manual changes.

Rebuild.

Redeploy.

Instead.

---

# 72. Container Image Scanning

Container images should be scanned.

Before deployment.

For:

- Known vulnerabilities
- Malware
- Embedded secrets
- License issues

---

# Image Pipeline

```text
Source Code

↓

Build Image

↓

Security Scan

↓

Approve

↓

Registry

↓

Deployment
```

Never deploy.

Unscanned images.

---

# Trusted Registries

Use trusted container registries.

Avoid downloading arbitrary images from unknown publishers.

---

# Image Signing

Images can be digitally signed.

Deployment systems verify signatures.

Before running workloads.

This helps protect the software supply chain.

---

# 73. Kubernetes Security

Kubernetes provides orchestration.

It also introduces.

New security challenges.

---

# Kubernetes Components

```text
Cluster

├── API Server
├── etcd
├── Scheduler
├── Nodes
├── Pods
└── Controllers
```

Every component requires protection.

---

# API Server Security

API Server.

Controls the cluster.

Protect with:

- Authentication
- Authorization
- RBAC
- Audit logging
- Network restrictions

---

# RBAC

Kubernetes RBAC controls.

Who can:

- Create Pods
- Delete Deployments
- View Secrets
- Manage Nodes

Avoid granting cluster-admin broadly.

---

# Pod Security

Pods should:

- Run as non-root
- Drop unnecessary Linux capabilities
- Use read-only filesystems where practical
- Limit host access

---

# Network Policies

By default.

Many Kubernetes networking implementations allow broad pod-to-pod communication.

Network Policies restrict traffic.

Example.

```text
Order Service

↓

Inventory Service

Allowed

------------------

Order Service

↓

HR Service

Denied
```

---

# Secrets in Kubernetes

Avoid storing plaintext secrets directly in manifests.

Prefer integrating Kubernetes with external secret management systems where appropriate.

---

# etcd Security

etcd stores.

Cluster state.

Including.

Secrets.

Protect with:

- Encryption
- Authentication
- Restricted network access
- Backups

---

# Admission Controllers

Admission Controllers validate.

Requests.

Before resources are created.

Examples.

Reject.

- Privileged containers
- Latest image tags
- Missing resource limits

They enforce organizational policies.

---

# 74. Runtime Security

Security continues.

After deployment.

---

# Runtime Monitoring

Detect:

- Unexpected processes
- Suspicious network connections
- Privilege escalation
- Crypto mining
- Malware
- Container escapes

---

# Behavioral Detection

Rather than relying only on signatures,

modern platforms monitor behavior.

Example.

Container suddenly executes.

```
bash
```

Although.

It normally runs.

Java.

Potentially suspicious.

---

# 75. Cloud Security Best Practices

Architects should ensure:

- Least privilege IAM
- MFA
- Encryption everywhere
- Secure networking
- Secret management
- Logging
- Monitoring
- Automated patching
- Image scanning
- Continuous compliance

Security should be automated whenever possible.

---

# Cloud Security Architecture

```text
Internet

↓

DDoS Protection

↓

WAF

↓

Load Balancer

↓

API Gateway

↓

Kubernetes

↓

Microservices

↓

Database

↓

Encrypted Storage
```

Supporting services.

```text
IAM

Secrets Manager

Logging

Monitoring

Key Management

SIEM
```

---

# Cloud Misconfiguration Examples

Common examples include:

- Public storage buckets
- Overly permissive IAM roles
- Open security groups
- Unencrypted databases
- Disabled logging
- Hardcoded credentials

Most cloud breaches result from configuration mistakes rather than cloud platform failures.

---

# Compliance

Many industries require compliance with standards such as:

- PCI DSS
- HIPAA
- GDPR
- SOC 2
- ISO 27001

Architects should understand applicable regulatory requirements before designing systems.

---

# Common Mistakes

### Using Administrator Permissions Everywhere

Convenient.

But dangerous.

Apply least privilege.

---

### Hardcoding Cloud Credentials

Use IAM roles or managed identities whenever possible.

---

### Public Databases

Production databases should not be directly reachable from the Internet.

---

### Ignoring Logs

Cloud audit logs provide essential forensic information during incidents.

Enable and retain them appropriately.

---

### Trusting Default Configurations

Secure defaults have improved significantly.

However,

every cloud deployment should still undergo security review.

---

# Architect's Perspective

Cloud platforms provide powerful security capabilities.

But they do not automatically produce secure systems.

Successful cloud architects design systems where:

- Identity is the primary security boundary.
- Every workload has a unique identity.
- Secrets are centrally managed.
- Networks are segmented.
- Containers are continuously scanned.
- Kubernetes policies are enforced.
- Storage is encrypted.
- Audit logs are always available.
- Security controls are automated.

The most secure cloud environments are not those with the most tools.

They are the ones with **consistent architecture, strong governance, least privilege, and continuous verification**.

---

**End of Part 8**

The next part will cover:

- **DevSecOps**
- **Shift Left Security**
- **SAST**
- **DAST**
- **SCA**
- **IaC Security**
- **Secret Scanning**
- **SBOM**
- **Software Supply Chain Security**
- **CI/CD Security**
- **Artifact Signing**
