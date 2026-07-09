
# 44. API Security Deep Dive

> **API Security is the practice of protecting APIs from unauthorized access, abuse, data leakage, and attacks while ensuring secure communication between clients and services.**

Today,

APIs are the primary attack surface for most enterprise applications.

Examples:

- Mobile Applications
- Web Applications
- Third-party Integrations
- IoT Devices
- AI Agents
- Microservices

If APIs are compromised,

the entire business is compromised.

---

# Why API Security Matters

Consider an online banking system.

```text
Customer

↓

Mobile App

↓

API Gateway

↓

Banking APIs

↓

Database
```

Every operation.

Depends on APIs.

- Login
- Balance Inquiry
- Fund Transfer
- Transaction History
- Card Management

If an attacker compromises the API,

they may gain direct access to business functions.

---

# API Threat Landscape

Modern APIs face threats including:

```text
API Threats

├── Broken Authentication
├── Broken Authorization
├── API Abuse
├── Replay Attacks
├── Token Theft
├── Injection
├── Excessive Data Exposure
├── Mass Assignment
├── Bot Traffic
├── Credential Stuffing
├── Rate Limit Bypass
└── Business Logic Abuse
```

Many of these appear in the OWASP API Security Top 10.

---

# Security Layers

API security should be implemented in layers.

```text
Client

↓

TLS

↓

API Gateway

↓

Authentication

↓

Authorization

↓

Input Validation

↓

Business Logic

↓

Audit Logging

↓

Monitoring
```

Never depend on a single security mechanism.

---

# 45. OAuth 2.0 Deep Dive

Earlier chapters introduced OAuth.

Now let's examine its architecture.

---

## What OAuth Solves

Imagine an application.

Needs access to:

Google Drive.

Without OAuth.

User shares.

Google password.

With application.

Dangerous.

OAuth eliminates this.

---

# OAuth Roles

OAuth defines four major participants.

```text
Resource Owner

↓

Client

↓

Authorization Server

↓

Resource Server
```

---

## Resource Owner

Usually.

The user.

Who owns the data.

---

## Client

Application requesting access.

Example.

Expense Management App.

---

## Authorization Server

Authenticates user.

Issues tokens.

Examples.

- Keycloak
- Auth0
- Microsoft Entra ID

---

## Resource Server

Hosts protected APIs.

Example.

Google Drive API.

---

# OAuth Flow (Authorization Code + PKCE)

```text
User

↓

Login

↓

Authorization Server

↓

Authorization Code

↓

Client

↓

Access Token

↓

API
```

This is the recommended flow for public clients such as mobile and SPA applications.

---

# Access Token

Short-lived.

Contains.

Authorization information.

Example.

```
15 Minutes
```

Lifetime.

---

# Refresh Token

Longer-lived.

Used to obtain.

New access tokens.

Without requiring the user to log in again.

---

# Why Short-Lived Tokens?

Suppose.

Access token stolen.

If valid.

Only.

15 minutes.

Damage limited.

---

# OAuth Grant Types

Modern OAuth primarily recommends:

- Authorization Code + PKCE
- Client Credentials
- Refresh Token

Legacy grant types such as the Implicit Grant are generally discouraged for new applications.

---

# Client Credentials

Machine-to-machine communication.

Example.

```text
Order Service

↓

Authentication

↓

Inventory Service
```

No user involved.

---

# 46. OpenID Connect (OIDC)

OAuth provides.

Authorization.

OIDC provides.

Authentication.

---

# OIDC Adds

- ID Token
- UserInfo Endpoint
- Identity Claims

Applications learn.

Who authenticated.

---

# ID Token

Typical claims.

```json
{
 "sub":"12345",
 "email":"john@example.com",
 "name":"John"
}
```

Contains.

Identity.

Not authorization.

---

# OAuth vs OIDC

| OAuth | OIDC |
|--------|------|
| Authorization | Authentication |
| Access Token | Access + ID Token |
| API Access | User Identity |

---

# 47. JWT Best Practices

JWT.

JSON Web Token.

Widely used.

For stateless authentication.

---

# JWT Structure

```text
Header

.

Payload

.

Signature
```

---

# Never Store

- Passwords
- Credit Cards
- Secrets

Inside JWT.

Payload is encoded.

Not encrypted.

---

# Validate Every JWT

Applications should verify:

- Signature
- Expiration
- Issuer
- Audience
- Algorithm
- Not Before (nbf) when present

Never trust an unvalidated token.

---

# Short Expiration

Bad.

```
30 Days
```

---

Better.

```
15 Minutes
```

Combined.

With refresh tokens.

---

# JWT Revocation

JWTs are stateless.

Revocation requires additional mechanisms.

Examples.

- Token blacklist
- Short expiration
- Token introspection
- Refresh token rotation

Architects should plan for token revocation during design.

---

# 48. API Keys

> **API Keys identify calling applications rather than end users.**

Example.

```http
X-API-Key:

ABC123XYZ
```

---

# Good Use Cases

- Public developer APIs
- Internal automation
- Third-party integrations
- Service accounts (when appropriate)

---

# Limitations

API Keys.

Do not provide.

User identity.

Or fine-grained authorization.

Sensitive systems usually require stronger mechanisms.

---

# API Key Best Practices

- Rotate regularly
- Restrict by IP where appropriate
- Scope permissions
- Monitor usage
- Never expose in frontend code

---

# 49. Replay Attack Protection

> **Replay attacks occur when an attacker captures a valid request and sends it again to repeat an operation.**

---

# Example

Payment API.

```
Transfer

₹50,000
```

Captured.

Replayed.

Ten times.

Money transferred.

Repeatedly.

---

# Prevention

- Nonces
- Timestamps
- Short-lived tokens
- TLS
- Idempotency Keys
- Request signatures

---

# Nonce

A nonce is.

A unique random value.

Used once.

Server rejects.

Repeated nonce values.

---

# Timestamp Validation

Request contains.

```
Timestamp

10:01 AM
```

If replayed.

At.

10:20 AM.

Reject.

Outside acceptable time window.

---

# 50. Idempotency Keys

Critical APIs.

Should support.

Idempotency.

Especially:

- Payments
- Orders
- Financial Transactions

---

# Example

Client sends.

```http
POST

/payments

Idempotency-Key:

XYZ123
```

---

If network fails.

Client retries.

Server recognizes.

Same key.

Returns previous result.

Instead of processing again.

---

# Benefits

- Safe retries
- Duplicate prevention
- Better user experience

---

# 51. Token Lifecycle Management

Tokens have.

A lifecycle.

```text
Issue

↓

Use

↓

Refresh

↓

Revoke

↓

Expire
```

Every stage.

Must be managed.

---

# Access Token

Short-lived.

Frequent use.

---

# Refresh Token

Long-lived.

Stored securely.

Used sparingly.

---

# Rotation

Refresh tokens should be rotated after successful use where supported.

If an old refresh token is reused unexpectedly,

it may indicate compromise.

---

# Logout

Logging out should:

- Invalidate refresh tokens
- Clear client storage
- Terminate sessions
- Revoke server-side state where applicable

---

# 52. API Gateway Security

API Gateway is often the first security enforcement point.

---

# Responsibilities

```text
Gateway

├── TLS
├── Authentication
├── Authorization
├── Rate Limiting
├── WAF Integration
├── Logging
├── Monitoring
├── IP Filtering
├── Request Validation
└── API Version Enforcement
```

---

# Request Validation

Gateway should reject.

Malformed requests.

Before.

Business services.

Receive them.

---

# IP Filtering

Examples.

Allow.

```
Corporate IP

Partner IP
```

Block.

Unknown sources.

Where business requirements permit.

---

# Geo Restrictions

Some organizations.

Restrict access.

To specific countries.

Based on.

Business.

Compliance.

Or fraud prevention.

---

# 53. Bot Protection

Not every client.

Is human.

Bots perform.

- Credential stuffing
- Scraping
- Inventory hoarding
- Price monitoring
- Spam
- DDoS

---

# Bot Detection

Signals include.

- Request frequency
- Device fingerprint
- IP reputation
- Behavioral analysis
- CAPTCHA (where appropriate)
- User-Agent anomalies

---

# Credential Stuffing

Attackers use.

Previously leaked.

Usernames.

Passwords.

Across many websites.

---

# Protection

- MFA
- Rate limiting
- IP reputation
- Password breach detection
- Adaptive authentication

---

# Mass Assignment

Some APIs automatically bind.

Incoming JSON.

To domain objects.

Example.

Client sends.

```json
{
 "name":"John",
 "role":"ADMIN"
}
```

If server blindly maps fields,

the user may elevate privileges.

---

# Prevention

Use.

DTOs.

Explicit field mapping.

Never bind domain entities directly from untrusted input.

---

# Excessive Data Exposure

API returns.

Entire customer object.

```json
{
 "name":"",
 "email":"",
 "salary":"",
 "ssn":"",
 "passwordHash":""
}
```

Client only needed.

Name.

Return.

Only required fields.

---

# Security Testing

API security should include:

- Authentication testing
- Authorization testing
- Penetration testing
- Fuzz testing
- Rate-limit testing
- Contract validation
- Dependency scanning

Security testing should be part of CI/CD.

---

# API Audit Logging

Log.

- Login
- Token issuance
- Token revocation
- Failed authentication
- Authorization failures
- Sensitive operations
- Administrative actions

Avoid logging sensitive secrets.

---

# Common Mistakes

### Long-Lived JWTs

Increase risk if compromised.

Prefer short-lived access tokens.

---

### No Idempotency for Payments

Retries may create duplicate transactions.

Support idempotent processing.

---

### Exposing Sensitive Data

Return only the minimum information required.

---

### API Keys in Source Code

Never hardcode.

API keys.

Use secret management.

---

### Missing Authorization Checks

Authentication alone is insufficient.

Every protected resource requires authorization.

---

# Architect's Perspective

Modern enterprise security revolves around APIs.

Every API should assume:

- It is publicly discoverable.
- Attackers will automate requests.
- Credentials may eventually be stolen.
- Clients may behave maliciously.
- Business logic will be targeted.

Therefore, secure APIs combine:

- Strong identity
- Fine-grained authorization
- Short-lived credentials
- Idempotent operations
- Layered defenses
- Continuous monitoring
- Comprehensive audit trails

Security is not achieved by a single technology such as OAuth or JWT.

It is achieved through **multiple independent controls working together to reduce business risk while enabling trusted communication**.

---

**End of Part 6**

The next part will cover:

- **Network Security**
- **Firewalls**
- **Web Application Firewall (WAF)**
- **VPC**
- **Security Groups**
- **Network ACLs**
- **VPN**
- **Bastion Hosts**
- **Micro-segmentation**
- **Service Mesh Security**
- **DDoS Protection**
