
# 13. Identity Management (IAM)

> **Identity Management is the process of creating, managing, authenticating, authorizing, and governing digital identities throughout their lifecycle.**

Every modern enterprise manages identities for:

- Employees
- Customers
- Partners
- Services
- APIs
- Devices
- AI Agents

Without Identity Management,

security quickly becomes unmanageable.

---

# Why Identity Management?

Imagine a company with:

- 25,000 Employees
- 800 Applications
- 5 Cloud Providers
- Thousands of APIs

Without centralized identity management,

every application stores:

- Users
- Passwords
- Roles
- Permissions

Problems arise.

- Duplicate accounts
- Weak passwords
- Difficult onboarding
- Difficult offboarding
- Security gaps

---

# IAM Solution

```text
User

↓

Identity Provider (IdP)

↓

Authentication

↓

Access Token

↓

Applications
```

Applications trust.

Identity Provider.

Instead of managing users independently.

---

# Identity Lifecycle

Identity management covers.

```text
Create User

↓

Assign Roles

↓

Authenticate

↓

Authorize

↓

Audit

↓

Disable

↓

Delete
```

This lifecycle applies to both humans and machines.

---

# Identity Types

Modern enterprises manage multiple identities.

```text
Human Identities

├── Employees
├── Customers
├── Vendors
└── Contractors

---------------------

Machine Identities

├── Microservices
├── APIs
├── Containers
├── Kubernetes Pods
├── Databases
└── IoT Devices
```

Machine identities now outnumber human identities in many organizations.

---

# Identity Provider (IdP)

> **An Identity Provider authenticates users and issues identity information that other applications trust.**

Popular examples include:

- Microsoft Entra ID (formerly Azure AD)
- Okta
- Keycloak
- Ping Identity
- Auth0
- AWS IAM Identity Center

Applications delegate authentication to the IdP.

---

# Benefits

- Centralized identity
- Single password policy
- MFA
- Central audit
- Simplified user management

---

# Identity Repository

Identity information typically contains:

```text
User ID

Name

Email

Department

Manager

Groups

Roles

Status

Last Login
```

Identity becomes the source of truth.

---

# Identity Governance

Governance ensures.

- Correct permissions
- Regular reviews
- Timely removal of access
- Compliance
- Auditability

---

# Joiner-Mover-Leaver Process

Enterprise IAM follows.

```text
Joiner

↓

Employee Created

↓

Roles Assigned

------------------

Mover

↓

Department Change

↓

Permissions Updated

------------------

Leaver

↓

Account Disabled

↓

Access Revoked
```

Automating this process reduces security risk.

---

# Machine Identity

Modern architectures must also authenticate.

- Services
- Containers
- Pods
- Serverless Functions

Example.

```text
Order Service

↓

Certificate

↓

Payment Service
```

Machine identity is foundational to Zero Trust.

---

# 14. Authentication

> **Authentication verifies the identity of a user, application, or service before granting access.**

Authentication answers.

```
Who are you?
```

Without authentication.

Nothing else matters.

---

# Authentication Flow

```text
User

↓

Credentials

↓

Identity Provider

↓

Verified

↓

Token

↓

Application
```

---

# Authentication Methods

```text
Knowledge

↓

Password

PIN

-------------------

Possession

↓

OTP

Mobile Phone

Smart Card

Security Key

-------------------

Inherence

↓

Fingerprint

Face ID

Iris Scan
```

These correspond to the three common authentication factors.

---

# Password Authentication

Still widely used.

Despite weaknesses.

Requirements.

- Long passwords
- Password hashing
- Rotation (where appropriate)
- Breach detection
- MFA

Never store.

Plain text passwords.

---

# Password Storage

Bad.

```text
Password

↓

Database
```

---

Good.

```text
Password

↓

Salt

↓

Hash

↓

Database
```

Passwords should be hashed using modern password hashing algorithms designed for credential storage.

---

# One-Time Password (OTP)

Temporary password.

Valid.

For a short duration.

Examples.

- SMS OTP
- Email OTP
- Authenticator App

---

# Hardware Security Keys

Examples.

- FIDO2
- WebAuthn
- YubiKey

Provide phishing-resistant authentication.

Recommended for privileged accounts.

---

# Biometric Authentication

Examples.

- Face Recognition
- Fingerprint
- Iris Scan

Advantages.

- Convenient
- Difficult to share

Limitations.

Should be combined with secure device protections.

---

# Multi-Factor Authentication (MFA)

> **MFA requires two or more independent authentication factors before granting access.**

Example.

```text
Password

+

OTP
```

Or.

```text
Password

+

Security Key
```

---

# Why MFA?

Password stolen.

Attacker.

Still needs.

Second factor.

Greatly reduces account compromise.

---

# MFA Examples

Bank Login.

```text
Username

↓

Password

↓

OTP

↓

Account Access
```

---

Cloud Console.

```text
Password

↓

Security Key

↓

Access
```

---

# Adaptive Authentication

Authentication decisions can change based on risk.

Example.

Trusted device.

Normal login.

---

Unknown device.

Different country.

Require.

MFA.

Or block access.

---

# Authentication Best Practices

- MFA
- Short-lived sessions
- Password hashing
- Device verification
- Login monitoring
- Account lockout
- Risk-based authentication

---

# 15. Authorization

Authentication proves identity.

Authorization determines permissions.

Question.

```
What are you allowed to do?
```

---

# Authorization Workflow

```text
Authenticate

↓

Identify Roles

↓

Evaluate Policies

↓

Grant or Deny
```

---

# Example

Customer.

Authenticated.

Requests.

```
DELETE

/users
```

Authorization.

Rejects.

Request.

---

# Fine-Grained Authorization

Instead of.

```
Admin

Customer
```

Modern systems evaluate.

- Resource ownership
- Region
- Department
- Time
- Device
- Risk score

Authorization becomes context-aware.

---

# 16. RBAC (Role-Based Access Control)

> **Permissions are assigned to roles, and users receive permissions by becoming members of those roles.**

---

# Example

```text
Administrator

├── Create User
├── Delete User
├── View Reports

-------------------

Customer

├── View Orders
├── Create Orders

-------------------

Support Agent

├── View Customers
├── Reset Password
```

---

# Advantages

- Simple
- Easy to understand
- Enterprise standard
- Scalable

---

# Limitations

Large organizations.

May accumulate.

Thousands.

Of roles.

Known as.

```
Role Explosion
```

---

# 17. ABAC (Attribute-Based Access Control)

> **Access decisions are based on attributes rather than fixed roles.**

Attributes include.

- Department
- Country
- Device
- Time
- Project
- Clearance
- Risk Score

---

# Example

Rule.

```text
Department = Finance

AND

Location = India

AND

Time < 6 PM
```

Access.

Granted.

---

# Advantages

- Flexible
- Dynamic
- Fine-grained

---

# Challenges

- Complex policies
- Harder debugging
- Governance complexity

---

# 18. PBAC (Policy-Based Access Control)

PBAC evaluates centralized policies.

Rather than hardcoded application rules.

---

Example.

```text
IF

Employee

AND

Manager Approval

AND

Working Hours

THEN

Allow
```

Policies can be updated without changing application code.

---

# 19. ACL (Access Control List)

Each resource.

Maintains its own permissions.

---

Example.

```text
Document A

↓

Alice

Read

↓

Bob

Read

Write

↓

Charlie

No Access
```

Widely used in:

- File systems
- Document management
- Collaboration platforms

---

# Authorization Model Comparison

| Model | Best Use Case |
|---------|---------------|
| RBAC | Enterprise Applications |
| ABAC | Dynamic Access Rules |
| PBAC | Centralized Policy Engines |
| ACL | Resource-Level Sharing |

---

# 20. Single Sign-On (SSO)

> **SSO allows users to authenticate once and access multiple applications without signing in again.**

---

# Without SSO

Employee logs into.

```text
Email

↓

HR

↓

CRM

↓

ERP

↓

Finance
```

Five logins.

---

# With SSO

```text
Employee

↓

Identity Provider

↓

All Applications
```

One login.

---

# Benefits

- Better user experience
- Centralized authentication
- Easier administration
- Stronger security

---

# Common SSO Protocols

- SAML 2.0
- OpenID Connect (OIDC)
- OAuth 2.0 (for delegated authorization)
- Kerberos (primarily in enterprise Windows environments)

---

# 21. Identity Federation

> **Identity Federation enables multiple organizations or systems to trust and share identity information without maintaining separate user accounts.**

---

# Example

Company A.

Employee.

Uses.

Corporate account.

To access.

Partner Portal.

No new account created.

---

# Federation Architecture

```text
User

↓

Identity Provider

↓

Federation

↓

Partner Application
```

Trust.

Established.

Between organizations.

---

# Benefits

- No duplicate accounts
- Better user experience
- Centralized identity
- Easier compliance

---

# Provisioning

Authentication.

Determines.

Who you are.

Provisioning determines.

What accounts exist.

Common standards.

- SCIM
- LDAP
- Directory synchronization

Provisioning automates:

- Account creation
- Role assignment
- Access removal

---

# Common Mistakes

### Treating Authentication as Authorization

Knowing who a user is does not determine what they may access.

Always evaluate permissions separately.

---

### Storing Passwords Incorrectly

Passwords should never be stored in plain text.

Use secure password hashing algorithms.

---

### No MFA for Privileged Users

Administrative accounts should always require stronger authentication.

---

### Hardcoding Permissions

Authorization rules should be centralized and maintainable.

Avoid scattering access checks throughout the codebase.

---

### Ignoring Machine Identity

Microservices, containers, and workloads also require authentication.

Identity is not only for people.

---

# Architect's Perspective

Identity has become the **new security perimeter**.

In cloud-native systems,

there is often no trusted internal network.

Instead, every request depends on:

- Identity
- Authentication
- Authorization
- Context
- Risk

Successful architects design systems where both **humans and machines possess verifiable identities**, permissions are granted according to business need, and every access decision is continuously evaluated.

Identity is the foundation upon which Zero Trust Architecture is built.

---

**End of Part 3**

The next part will cover:

- **Cryptography Fundamentals**
- **Symmetric Encryption**
- **Asymmetric Encryption**
- **Hashing**
- **Digital Signatures**
- **PKI**
- **TLS & mTLS**
- **Certificates**
- **Key Management**
- **Secrets Management**
- **Hardware Security Modules (HSM)**
