
# 27. API Security

> **API Security is the practice of protecting APIs from unauthorized access, data breaches, abuse, and attacks while ensuring confidentiality, integrity, and availability.**

Modern enterprises expose APIs to:

- Customers
- Mobile Applications
- Third-Party Partners
- Internal Microservices
- AI Agents
- IoT Devices

APIs are often the **primary attack surface** of an application.

Therefore,

API Security is one of the most critical responsibilities of a Solution Architect.

---

# Why API Security Matters

Imagine a Banking API.

```text
POST

/transferMoney
```

Without authentication.

Anyone.

Can transfer money.

---

Imagine an E-Commerce API.

```
GET

/orders
```

Returns.

Every customer's orders.

Without authorization.

Massive data breach.

---

# Security Objectives

API Security protects.

```text
Confidentiality

↓

Integrity

↓

Availability
```

Also known as.

```
CIA Triangle
```

---

# Common API Threats

```text
API Threats

├── Broken Authentication
├── Broken Authorization
├── Injection Attacks
├── Data Exposure
├── Rate Limit Abuse
├── DoS/DDoS
├── Replay Attacks
├── Credential Theft
├── API Abuse
└── Bot Traffic
```

---

# Security Layers

Enterprise API Security consists of multiple layers.

```text
Client

↓

Authentication

↓

Authorization

↓

Validation

↓

Rate Limiting

↓

Business Logic

↓

Audit Logging
```

Never rely on.

One security layer.

---

# 28. Authentication

> **Authentication answers the question: "Who are you?"**

Authentication verifies.

Identity.

Examples.

- Username/Password
- OAuth
- JWT
- API Key
- Certificate
- Biometrics

---

# Authentication Flow

```text
User

↓

Login

↓

Identity Verified

↓

Access Token

↓

API Calls
```

---

# Real Example

Customer logs in.

Receives.

```
JWT Token
```

Future requests.

Include.

```
Authorization:

Bearer TOKEN
```

---

# Authentication Factors

Authentication can use.

### Something You Know

- Password
- PIN

---

### Something You Have

- Mobile Phone
- Security Token
- Smart Card

---

### Something You Are

- Fingerprint
- Face ID
- Iris Scan

---

# Multi-Factor Authentication (MFA)

Enterprise systems often combine factors.

Example.

```text
Password

+

OTP

+

Fingerprint
```

Much stronger than password alone.

---

# 29. Authorization

> **Authorization answers the question: "What are you allowed to do?"**

Authentication.

Comes first.

Authorization.

Comes second.

---

# Example

User authenticated.

But.

Can they access?

```
DELETE

/admin/users
```

Authorization decides.

---

# Workflow

```text
Authenticate User

↓

Check Roles

↓

Check Permissions

↓

Allow / Deny
```

---

# Authentication vs Authorization

| Authentication | Authorization |
|----------------|---------------|
| Who are you? | What can you do? |
| Identity | Permissions |
| Login | Access Control |
| First Step | Second Step |

---

# Authorization Models

```text
Authorization

├── RBAC
├── ABAC
├── ACL
└── PBAC
```

---

# RBAC (Role-Based Access Control)

Users assigned.

Roles.

Roles contain.

Permissions.

---

Example.

```text
Admin

↓

Create User

Delete User

View Reports

--------------

Customer

↓

View Orders

Create Orders
```

Most enterprise applications use RBAC.

---

# Advantages

- Simple
- Easy administration
- Scalable

---

# Limitations

Difficult for highly dynamic business rules.

---

# ABAC (Attribute-Based Access Control)

Access depends on.

Attributes.

Examples.

- Department
- Country
- Time
- Device
- Customer Tier

---

Example.

```
Managers

Can Approve

Only During

Business Hours
```

Much more flexible.

---

# ACL (Access Control List)

Each resource.

Stores.

Allowed users.

Example.

```
Document

↓

User1

Read

↓

User2

Write
```

Useful.

For document sharing.

---

# PBAC (Policy-Based Access Control)

Policies determine.

Access.

Often implemented using dedicated policy engines.

Example.

```
Age >18

AND

Country=India

AND

Subscription=Premium
```

Access granted.

---

# Authorization Comparison

| Model | Best For |
|--------|-----------|
| RBAC | Enterprise Applications |
| ABAC | Dynamic Rules |
| ACL | File Sharing |
| PBAC | Complex Compliance |

---

# Principle of Least Privilege

One of the most important security principles.

> **Users should receive only the minimum permissions required to perform their work.**

Never.

Give.

```
Admin

Access

To Everyone.
```

---

# 30. API Keys

> **An API Key is a unique identifier used to authenticate an application making API requests.**

Typically used.

For:

- Third-party APIs
- Internal integrations
- Public developer platforms

---

# Example

```http
GET /weather

X-API-Key:

ABC123XYZ
```

---

# Advantages

- Simple
- Easy integration
- Low overhead

---

# Limitations

API Keys identify.

Applications.

Not users.

Alone they are usually insufficient for sensitive operations.

---

# Best Practices

- Rotate keys
- Store securely
- Never expose in frontend code
- Combine with TLS
- Apply rate limits

---

# 31. OAuth 2.0

> **OAuth 2.0 is an authorization framework that allows applications to access user resources without sharing user passwords.**

OAuth is about.

Delegated authorization.

Not authentication.

---

# Real Example

Login using.

```
Google
```

Application never sees.

Google password.

---

# OAuth Participants

```text
User

↓

Client

↓

Authorization Server

↓

Resource Server
```

---

# OAuth Flow (Simplified)

```text
User

↓

Login

↓

Authorization Server

↓

Access Token

↓

Client

↓

API
```

---

# Common OAuth Grant Types

Modern OAuth primarily uses:

- Authorization Code + PKCE
- Client Credentials
- Refresh Token

Some older grant types exist but are no longer recommended for new applications.

---

## Authorization Code + PKCE

Best for:

- Web Applications
- Mobile Applications
- Single Page Applications (SPA)

Most secure interactive flow.

---

## Client Credentials

Application.

Authenticates.

Itself.

No user.

Typical.

Microservices.

---

## Refresh Token

Long-lived token.

Used.

To obtain.

New access tokens.

Without logging in again.

---

# OAuth Benefits

- No password sharing
- Standardized
- Secure delegation
- Widely supported

---

# 32. OpenID Connect (OIDC)

> **OpenID Connect is an authentication layer built on top of OAuth 2.0.**

OAuth.

Provides authorization.

OIDC.

Provides authentication.

---

# OIDC Returns

- Access Token
- ID Token
- User Information

---

# ID Token

Contains.

Identity.

Example.

```
User

Email

Roles

Subject

Issuer
```

---

# OAuth vs OIDC

| OAuth | OIDC |
|--------|------|
| Authorization | Authentication |
| Access Token | Access + ID Token |
| API Access | User Identity |

---

# 33. JWT (JSON Web Token)

> **JWT is a compact, digitally signed token used to securely transmit identity and authorization information between systems.**

JWT is.

Stateless.

---

# JWT Structure

```text
Header

.

Payload

.

Signature
```

Three parts.

Separated by.

```
.
```

---

# Example

```text
xxxxx.yyyyy.zzzzz
```

---

# Header

Contains.

```json
{
 "alg":"RS256",
 "typ":"JWT"
}
```

---

# Payload

Contains.

Claims.

```json
{
 "sub":"101",
 "role":"ADMIN",
 "exp":123456789
}
```

---

# Signature

Protects.

Against.

Tampering.

---

# JWT Workflow

```text
Login

↓

JWT

↓

Client Stores

↓

Authorization Header

↓

API Validates
```

---

# Advantages

- Stateless
- Fast
- Scalable
- No server session required

---

# Challenges

- Revocation
- Expiration management
- Token leakage
- Large payload misuse

---

# JWT Best Practices

- Short expiration
- Use HTTPS
- Never store secrets in payload
- Validate signature
- Validate issuer
- Validate audience
- Validate expiration

---

# 34. Mutual TLS (mTLS)

> **Mutual TLS authenticates both the client and the server using digital certificates.**

Normal HTTPS.

Authenticates.

Server.

---

mTLS.

Authenticates.

Both.

Client.

And.

Server.

---

# Architecture

```text
Client Certificate

↓

Server Certificate

↓

Secure Communication
```

---

# Enterprise Uses

- Banking
- Internal Microservices
- Healthcare
- Government
- Financial Trading

---

# Benefits

- Strong identity
- Certificate-based trust
- Resistant to password theft

---

# 35. Cross-Origin Resource Sharing (CORS)

Browsers enforce:

Same-Origin Policy.

Example.

```
app.company.com
```

Cannot automatically call.

```
api.othercompany.com
```

Without permission.

---

> **CORS allows servers to specify which origins are permitted to access an API.**

---

# Example

```http
Access-Control-Allow-Origin:

https://app.company.com
```

---

# Common Mistake

Never configure.

```http
Access-Control-Allow-Origin: *
```

For authenticated APIs.

Unless there is a specific and safe reason.

---

# 36. Cross-Site Request Forgery (CSRF)

> **CSRF tricks a user's browser into performing unwanted actions on another site where the user is already authenticated.**

---

# Example

User logged into.

Bank.

Visits malicious website.

Hidden request.

Transfers money.

Without user knowledge.

---

# Protection

- CSRF Tokens
- SameSite Cookies
- Origin validation
- Referer validation

---

# JWT APIs

Stateless APIs using Authorization headers are generally less susceptible to traditional CSRF attacks than cookie-based session authentication, but overall application design still matters.

---

# 37. Input Validation

Never trust.

Client input.

---

Validate.

- Length
- Type
- Format
- Required fields
- Range
- Business rules

---

# Example

Bad.

```
Age=-100
```

Should fail.

Validation.

---

# SQL Injection

Bad.

```sql
SELECT *

WHERE

name='

OR

1=1
```

Never concatenate.

SQL.

Use parameterized queries.

---

# XSS

User submits.

```html
<script>

alert()

</script>
```

Sanitize.

Output.

---

# File Upload Validation

Validate.

- Size
- Extension
- MIME type
- Virus scan
- Storage location

Never trust the file name alone.

---

# 38. Rate Limiting & Throttling

> **Rate Limiting controls how many requests a client can make within a given time period to protect APIs from abuse and ensure fair resource usage.**

Examples.

```
100 Requests

Per Minute
```

---

# Why?

Protect.

Against.

- Abuse
- Bots
- DoS
- Excessive usage
- Cost spikes

---

# Example

```text
Client

↓

100 Requests

↓

Allowed

---------------

101st Request

↓

429 Too Many Requests
```

---

# Throttling

Throttling slows requests.

Rather than immediately rejecting every request.

Implementation varies by platform and business requirements.

---

# Common Algorithms

- Token Bucket
- Leaky Bucket
- Sliding Window
- Fixed Window

We covered these in the dedicated Rate Limiting chapter.

---

# Security Checklist

Before exposing an API.

Verify.

- Authentication
- Authorization
- TLS
- Input validation
- Rate limiting
- Logging
- Audit trail
- Secrets management
- Error handling
- Monitoring

---

# Common Mistakes

### Authentication Without Authorization

Knowing who the user is does not mean they should access every resource.

---

### Long-Lived JWTs

Long expiration increases risk if tokens are compromised.

Prefer short-lived access tokens with refresh tokens where appropriate.

---

### Sensitive Data in JWT Payload

JWT payloads are encoded, not encrypted.

Never place passwords or secrets inside them.

---

### Accepting All Origins

Avoid overly permissive CORS policies for protected APIs.

---

### Trusting Client Validation

Client-side validation improves user experience.

Server-side validation protects security.

Always validate on the server.

---

# Architect's Perspective

API Security is built using **multiple independent layers**.

No single mechanism—OAuth, JWT, API Keys, or TLS—solves every security problem.

A secure enterprise API combines:

- Strong authentication
- Fine-grained authorization
- Secure transport
- Input validation
- Rate limiting
- Audit logging
- Continuous monitoring

Architects should always assume:

> **Every public API endpoint will eventually be probed or attacked.**

Designing APIs with a **Zero Trust** mindset significantly reduces business risk and improves long-term resilience.

---

**End of Part 4B**

The next part will cover:

- **API Gateway**
- **Why API Gateway**
- **Routing**
- **Authentication**
- **Rate Limiting**
- **Caching**
- **Request Aggregation**
- **Load Balancing**
- **Service Discovery**
- **Backend for Frontend (BFF)**
- **API Composition**
