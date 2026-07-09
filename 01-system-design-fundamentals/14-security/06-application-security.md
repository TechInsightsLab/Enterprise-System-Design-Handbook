
# 33. Application Security

> **Application Security is the practice of designing, developing, testing, deploying, and operating software in a manner that protects applications from security threats and vulnerabilities throughout their lifecycle.**

Application security is not one security feature.

It is a collection of practices.

Applied throughout the Software Development Life Cycle (SDLC).

---

# Why Application Security Matters

Most cyber attacks.

Do not target.

Operating systems.

They target.

Applications.

Examples.

- Banking Applications
- E-Commerce Websites
- Mobile Apps
- APIs
- Healthcare Portals

Because applications contain:

- Customer Data
- Payment Information
- Business Logic
- Authentication Systems

---

# Business Example

Imagine an E-Commerce Platform.

Architecture.

```text
Customer

↓

Web Application

↓

API Gateway

↓

Order Service

↓

Payment Service

↓

Database
```

Suppose.

One developer writes.

```sql
SELECT *

FROM USERS

WHERE NAME='"

+ input +

"'"
```

An attacker enters.

```sql
' OR 1=1 --
```

Entire customer database.

Exposed.

One insecure query.

Causes.

A massive breach.

---

# Secure Software Development Lifecycle (SSDLC)

Security should be integrated.

Into every stage.

```text
Requirements

↓

Architecture

↓

Development

↓

Testing

↓

Deployment

↓

Operations

↓

Monitoring
```

Security is continuous.

Not a final checklist.

---

# Secure Coding

> **Secure Coding is the practice of writing software that prevents vulnerabilities from being introduced into the application.**

Developers should assume.

Every external input.

Can be malicious.

---

# Secure Coding Principles

```text
Validate Input

↓

Encode Output

↓

Least Privilege

↓

Fail Secure

↓

Secure Defaults

↓

No Hardcoded Secrets

↓

Parameterized Queries
```

---

# Never Trust User Input

Every external source.

Should be considered untrusted.

Examples.

- HTTP Requests
- Headers
- Cookies
- Query Parameters
- File Uploads
- API Requests
- Kafka Messages
- User Forms

Validate.

Everything.

---

# Input Validation

> **Input Validation ensures that incoming data conforms to expected format, size, type, and business rules before processing.**

---

# Validate

- Required fields
- Length
- Type
- Range
- Allowed values
- Regular expressions
- Business constraints

---

# Example

Age.

```
-100
```

Reject.

---

Email.

```
abc@
```

Reject.

---

Quantity.

```
99999999999
```

Validate.

Against.

Business limits.

---

# Whitelist vs Blacklist Validation

## Blacklist

Reject.

Known bad input.

Example.

```
DROP
```

Problem.

Attackers invent.

New patterns.

---

## Whitelist

Allow.

Only expected input.

Example.

```
Only Numbers

0-9
```

Much safer.

---

# Server-Side Validation

Client-side validation improves.

User experience.

But.

Can be bypassed.

Always perform.

Server-side validation.

---

# Output Encoding

> **Output Encoding prevents untrusted data from being interpreted as executable code by the client.**

Especially important.

For browsers.

---

# Example

User submits.

```html
<script>

alert("Hacked")

</script>
```

Without encoding.

Browser executes.

JavaScript.

With encoding.

Browser displays.

Text.

Safely.

---

# OWASP Top 10

OWASP publishes.

The most critical.

Application security risks.

Every architect.

Should understand them.

---

# Current Categories

```text
OWASP

├── Broken Access Control
├── Cryptographic Failures
├── Injection
├── Insecure Design
├── Security Misconfiguration
├── Vulnerable Components
├── Authentication Failures
├── Software Integrity Failures
├── Logging Failures
└── Server-Side Request Forgery
```

These categories evolve over time as threats change.

---

# 34. SQL Injection

> **SQL Injection occurs when untrusted input is interpreted as SQL commands by the database.**

One of the oldest.

Yet still common.

Attacks.

---

# Vulnerable Code

```java
String sql =
"SELECT * FROM USERS WHERE NAME='"

+ username +

"'";
```

User input becomes.

Part.

Of SQL.

Dangerous.

---

# Attack

Input.

```sql
' OR 1=1 --
```

Query becomes.

```sql
SELECT *

FROM USERS

WHERE NAME=''

OR

1=1
```

Returns.

Every user.

---

# Consequences

Attackers may:

- Read data
- Modify data
- Delete data
- Execute database commands
- Bypass authentication

---

# Prevention

Always use.

Parameterized queries.

Prepared statements.

ORM frameworks correctly.

Input validation.

Least privilege database accounts.

---

# Example

```java
PreparedStatement

?

Instead of

String Concatenation
```

---

# 35. NoSQL Injection

NoSQL databases.

Can also be vulnerable.

If queries are constructed.

From untrusted input.

---

# Example

MongoDB.

Improper query construction.

May allow.

Authentication bypass.

---

# Prevention

- Validate input
- Use safe query APIs
- Avoid dynamic query construction

---

# 36. Command Injection

> **Command Injection occurs when user input is executed as operating system commands.**

---

# Example

Application runs.

```bash
ping

+

UserInput
```

Attacker enters.

```bash
google.com

&&

rm -rf /
```

Potentially catastrophic.

---

# Prevention

- Avoid shell execution
- Use APIs instead
- Validate input
- Escape arguments
- Least privilege

---

# 37. Cross-Site Scripting (XSS)

> **XSS allows attackers to inject malicious JavaScript into pages viewed by other users.**

---

# Example

User posts.

```html
<script>

stealCookies()

</script>
```

Another customer opens.

Page.

Script executes.

---

# Types

```text
XSS

├── Stored
├── Reflected
└── DOM-Based
```

---

# Stored XSS

Malicious script.

Stored.

Database.

Executed.

Whenever page loads.

Most dangerous.

---

# Reflected XSS

Malicious script.

Included.

In request.

Immediately reflected.

In response.

---

# DOM XSS

JavaScript.

On client.

Processes untrusted data.

Resulting in code execution.

---

# Prevention

- Output encoding
- Content Security Policy (CSP)
- Input validation
- Secure frameworks
- Avoid unsafe DOM manipulation

---

# 38. Cross-Site Request Forgery (CSRF)

> **CSRF tricks a victim's browser into sending unintended authenticated requests to another site.**

---

# Example

Customer logged into.

Bank.

Visits.

Malicious website.

Hidden form submits.

```text
Transfer Money
```

Bank.

Receives.

Authenticated request.

---

# Prevention

- CSRF Tokens
- SameSite Cookies
- Origin validation
- Referer validation

---

# JWT APIs

Stateless APIs using Authorization headers are generally less susceptible to traditional CSRF attacks than cookie-based session authentication.

---

# 39. Server-Side Request Forgery (SSRF)

> **SSRF tricks a server into making requests to unintended internal or external resources.**

---

# Example

Image API.

Accepts.

URL.

Attacker provides.

```
http://internal-database
```

Server.

Fetches.

Internal resource.

Attacker gains access.

---

# Prevention

- URL allowlists
- Network restrictions
- Metadata endpoint protection
- Disable unnecessary outbound access

---

# 40. XML External Entity (XXE)

> **XXE exploits XML parsers that process external entities.**

May expose.

- Files
- Internal systems
- Credentials

---

# Prevention

Disable.

External entity processing.

Use secure XML parser configurations.

---

# 41. Path Traversal

> **Path Traversal allows attackers to access files outside the intended directory.**

---

# Example

Application expects.

```
/images/photo.jpg
```

Attacker requests.

```
../../../etc/passwd
```

Without validation.

Sensitive files.

May be exposed.

---

# Prevention

- Canonicalize paths
- Restrict directories
- Validate filenames
- Use allowlists

---

# 42. File Upload Security

File uploads.

Appear simple.

But are high risk.

---

# Risks

- Malware
- Ransomware
- Web shells
- Large files
- Dangerous file types

---

# Validation Checklist

Verify.

- File size
- MIME type
- Extension
- Virus scan
- Storage location
- Ownership

---

# Never Trust

```
resume.pdf
```

File name alone.

Verify.

Actual content.

---

# Storage

Never store.

User uploads.

Inside executable application directories.

Use isolated storage.

---

# 43. Insecure Deserialization

> **Insecure Deserialization occurs when untrusted serialized data is deserialized without proper validation.**

Attackers may trigger.

- Remote code execution
- Privilege escalation
- Denial of service

---

# Prevention

- Avoid deserializing untrusted data
- Use safe serialization libraries
- Validate object types
- Apply integrity checks

---

# Secure Error Handling

Bad.

```text
SQL Exception

Stack Trace

Database Password

File Path
```

Displayed.

To customer.

---

Good.

Customer sees.

```
Unexpected Error

Please Contact Support
```

Detailed information.

Stored.

In logs.

Only.

---

# Security Headers

Modern web applications should consider headers such as:

- Content-Security-Policy
- X-Content-Type-Options
- Referrer-Policy
- Permissions-Policy
- Strict-Transport-Security (HSTS)

These reduce browser-based attack vectors.

---

# Dependency Security

Applications depend on:

- Open-source libraries
- Frameworks
- SDKs

A vulnerable dependency can compromise the entire application.

Regularly:

- Update dependencies
- Scan for vulnerabilities
- Remove unused libraries

---

# Secure Session Management

Sessions should:

- Expire automatically
- Be regenerated after login
- Use secure cookies when applicable
- Be invalidated on logout

Avoid predictable session identifiers.

---

# Common Mistakes

### Client-Side Validation Only

Attackers can bypass browser validation.

Always validate on the server.

---

### String Concatenation for SQL

Never build SQL queries using user input.

Use parameterized queries.

---

### Trusting File Extensions

```
invoice.pdf.exe
```

May not be a PDF.

Inspect actual file content.

---

### Displaying Internal Errors

Stack traces reveal implementation details.

Expose generic errors to users.

Log detailed diagnostics internally.

---

### Ignoring Third-Party Libraries

Applications inherit vulnerabilities from dependencies.

Keep dependencies updated and monitored.

---

# Architect's Perspective

Application Security is where most attackers focus because applications process valuable business data.

Architects should adopt the mindset:

> **Every input is malicious until proven otherwise.**

Design systems that:

- Validate every input.
- Encode every output.
- Authenticate every request.
- Authorize every action.
- Log every critical event.
- Reveal minimal information on failure.
- Assume dependencies may become vulnerable.

Secure applications are not created by adding a Web Application Firewall at the end.

They are created through **secure architecture, secure coding practices, continuous testing, and disciplined operational processes**.

---

**End of Part 5**

The next part will cover:

- **API Security Deep Dive**
- **OAuth 2.0**
- **OpenID Connect (OIDC)**
- **JWT Best Practices**
- **API Keys**
- **Replay Attack Protection**
- **Idempotency Keys**
- **Token Lifecycle Management**
- **API Gateway Security**
- **Bot Protection**
