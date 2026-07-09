
# 76. DevSecOps

> **DevSecOps is the practice of integrating security into every stage of the Software Development Lifecycle (SDLC), making security a shared responsibility across Development, Security, and Operations teams.**

Traditional software development often treated security as the final step before production.

Modern systems cannot afford this approach.

Security must be:

- Continuous
- Automated
- Integrated
- Measurable

---

# Why DevSecOps?

Traditional Development.

```text
Development

↓

Testing

↓

Deployment

↓

Security Review

↓

Production
```

Problems.

- Security discovered too late
- Expensive fixes
- Release delays
- Production vulnerabilities

---

# DevSecOps

```text
Plan

↓

Code

↓

Build

↓

Test

↓

Security Scan

↓

Deploy

↓

Monitor
```

Security becomes part of every stage.

---

# DevSecOps Principles

```text
Automation

↓

Continuous Security

↓

Shift Left

↓

Shared Responsibility

↓

Continuous Monitoring
```

---

# Benefits

- Faster releases
- Fewer vulnerabilities
- Lower remediation cost
- Better compliance
- Continuous visibility
- Improved developer awareness

---

# Security as a Shared Responsibility

Security is no longer owned only by the security team.

```text
Developer

↓

Security Engineer

↓

DevOps Engineer

↓

Solution Architect

↓

Operations
```

Everyone contributes.

---

# Security Throughout CI/CD

```text
Developer

↓

Commit Code

↓

Build

↓

Security Scan

↓

Unit Test

↓

Deploy

↓

Runtime Monitoring
```

Every pipeline execution validates security.

---

# 77. Shift Left Security

> **Shift Left Security means identifying and fixing security issues as early as possible during software development.**

Instead of.

Finding security issues.

During production.

Find them.

While writing code.

---

# Traditional Timeline

```text
Requirements

↓

Development

↓

Deployment

↓

Security Testing
```

Late detection.

High cost.

---

# Shift Left Timeline

```text
Requirements

↓

Threat Modeling

↓

Secure Coding

↓

Static Analysis

↓

Dependency Scan

↓

Deployment
```

Earlier detection.

Lower cost.

---

# Cost of Fixing Vulnerabilities

Generally.

The later a vulnerability is discovered,

the more expensive it becomes to fix.

Example.

```text
Design Phase

↓

Low Cost

-----------------

Production

↓

Very High Cost
```

---

# Architect's Responsibility

Architects should encourage:

- Secure design reviews
- Threat modeling
- Secure coding standards
- Automated security testing

Before implementation begins.

---

# 78. Static Application Security Testing (SAST)

> **SAST analyzes source code, bytecode, or binaries to identify security vulnerabilities without executing the application.**

SAST examines.

The code itself.

---

# Workflow

```text
Developer

↓

Commit Code

↓

SAST Scan

↓

Security Report
```

---

# Detects

- SQL Injection
- XSS
- Hardcoded Secrets
- Unsafe APIs
- Weak Cryptography
- Input Validation Issues

---

# Benefits

- Early detection
- Automated
- Integrated with IDEs
- Fast feedback

---

# Limitations

- Cannot detect runtime issues
- May generate false positives
- Business logic vulnerabilities may be missed

---

# Example

Developer writes.

```java
String sql =
"SELECT * FROM USER WHERE ID=" + id;
```

SAST identifies.

Potential SQL Injection.

Before deployment.

---

# 79. Dynamic Application Security Testing (DAST)

> **DAST tests a running application by simulating attacks from an external perspective.**

Unlike SAST.

DAST executes.

Against.

Running software.

---

# Workflow

```text
Deploy Test Environment

↓

DAST Scanner

↓

HTTP Requests

↓

Security Report
```

---

# Detects

- SQL Injection
- XSS
- Authentication Issues
- Misconfiguration
- Runtime Vulnerabilities
- Broken Access Control

---

# Advantages

- Realistic testing
- No source code required
- Finds runtime issues

---

# Limitations

- Requires deployed application
- Slower than SAST
- Limited code visibility

---

# SAST vs DAST

| SAST | DAST |
|-------|------|
| Source Code | Running Application |
| Early SDLC | Testing Phase |
| White Box | Black Box |
| Fast Feedback | Runtime Validation |

Best practice.

Use both.

---

# 80. Software Composition Analysis (SCA)

> **SCA identifies vulnerabilities in third-party libraries, frameworks, and open-source dependencies.**

Modern applications contain.

Thousands.

Of external packages.

---

# Example

```text
Spring Boot

↓

Jackson

↓

Logback

↓

Apache Commons

↓

Hundreds of Libraries
```

One vulnerable dependency.

Can compromise.

Entire application.

---

# Detects

- Known CVEs
- Outdated libraries
- License violations
- Dependency conflicts

---

# Famous Example

Log4Shell.

Affected millions.

Of applications.

That depended on.

Log4j.

Many organizations were vulnerable without modifying their own code.

---

# Best Practices

- Update dependencies
- Remove unused libraries
- Monitor CVEs
- Automate scanning

---

# 81. Infrastructure as Code (IaC) Security

Modern infrastructure.

Is defined.

Using code.

Examples.

- Terraform
- AWS CloudFormation
- Azure Bicep
- Kubernetes YAML

---

# Why Scan IaC?

Infrastructure misconfiguration.

Can expose.

Entire cloud environments.

---

# Example

Terraform.

Creates.

Public database.

IaC Scanner.

Detects.

Violation.

Before deployment.

---

# Detects

- Open Security Groups
- Public Storage
- Missing Encryption
- Weak IAM Policies
- Missing Logging

---

# Benefits

- Prevents insecure deployments
- Automated governance
- Compliance validation

---

# 82. Secret Scanning

Applications often accidentally contain:

- API Keys
- Passwords
- Certificates
- Tokens
- Cloud Credentials

---

# Example

Developer commits.

```java
AWS_SECRET_KEY

=

ABC123XYZ
```

Secret scanner.

Blocks commit.

---

# Common Locations

- Git repositories
- Dockerfiles
- YAML
- Properties files
- Terraform
- CI/CD Pipelines

---

# Best Practices

- Scan every commit
- Block exposed secrets
- Rotate leaked credentials immediately
- Use secret managers

---

# 83. Software Bill of Materials (SBOM)

> **An SBOM is a complete inventory of all software components, libraries, dependencies, and versions used within an application.**

Think of it as.

An ingredient list.

For software.

---

# Example

Application.

Contains.

```text
Spring Boot

Jackson

Netty

Logback

Kafka Client

OpenSSL
```

SBOM records.

Every dependency.

---

# Benefits

- Faster vulnerability response
- Compliance
- Supply chain visibility
- License management

---

# When New CVEs Appear

Suppose.

A vulnerability affects.

```
OpenSSL
```

SBOM immediately identifies.

Which applications.

Use it.

---

# 84. Software Supply Chain Security

Modern software depends on:

- Open-source libraries
- CI/CD systems
- Build servers
- Artifact repositories
- Container registries

Each introduces risk.

---

# Supply Chain Architecture

```text
Developer

↓

Git

↓

CI

↓

Build

↓

Artifact

↓

Container Registry

↓

Production
```

Every stage.

Must be trusted.

---

# Risks

- Compromised dependencies
- Malicious packages
- Build server compromise
- Artifact tampering
- Registry compromise

---

# Controls

- Dependency verification
- Image signing
- Artifact signing
- Access controls
- Immutable builds
- SBOM generation

---

# 85. CI/CD Pipeline Security

CI/CD pipelines have privileged access.

Examples.

- Production credentials
- Deployment keys
- Signing keys
- Cloud permissions

Protect them carefully.

---

# Secure Pipeline

```text
Developer

↓

Source Control

↓

Security Scan

↓

Build

↓

Test

↓

Artifact Signing

↓

Deploy
```

Security gates.

Prevent unsafe releases.

---

# Pipeline Best Practices

- Least privilege
- Isolated build agents
- Secret management
- Signed artifacts
- Audit logging
- Manual approval for production where appropriate

---

# 86. Artifact Signing

> **Artifact Signing verifies that software packages have not been modified after they were built.**

Artifacts include.

- JAR files
- Docker Images
- ZIP Packages
- Helm Charts

---

# Workflow

```text
Build

↓

Sign Artifact

↓

Publish

↓

Verify Signature

↓

Deploy
```

Deployment proceeds.

Only.

If signature is valid.

---

# Benefits

- Prevents tampering
- Supply chain protection
- Strong integrity guarantees

---

# Runtime Security Monitoring

Security continues.

After deployment.

Monitor for:

- Unauthorized logins
- Privilege escalation
- Secret access
- Suspicious API usage
- Unexpected container behavior
- Configuration drift

DevSecOps is continuous.

Not limited to CI/CD.

---

# Compliance Automation

Security controls should be automated wherever possible.

Examples.

- Password policy validation
- Encryption verification
- Secret scanning
- Dependency scanning
- Infrastructure compliance
- Container scanning

Automation reduces human error.

---

# Secure SDLC Architecture

```text
Requirements

↓

Threat Modeling

↓

Development

↓

SAST

↓

Build

↓

SCA

↓

Container Scan

↓

IaC Scan

↓

Artifact Signing

↓

Deployment

↓

Runtime Monitoring
```

Security exists.

At every stage.

---

# Common Mistakes

### Security Only Before Release

Security should be continuous.

Not a final approval step.

---

### Ignoring Open-Source Dependencies

Most applications rely heavily on third-party software.

Continuously monitor dependency vulnerabilities.

---

### Hardcoded Secrets in CI/CD

Pipelines should retrieve secrets securely at runtime.

---

### Skipping Artifact Verification

Unsigned artifacts increase supply chain risk.

Verify integrity before deployment.

---

### Treating Security as a Manual Process

Manual reviews are valuable.

But repetitive security controls should be automated.

---

# Architect's Perspective

DevSecOps transforms security from a **gatekeeper** into an **engineering capability**.

The architect's objective is not to create more manual approvals.

Instead, build delivery pipelines where:

- Secure code is encouraged.
- Vulnerabilities are detected automatically.
- Misconfigurations are prevented before deployment.
- Software integrity is verified.
- Runtime behavior is continuously monitored.

The most mature organizations achieve security by making **the secure path the easiest path** for developers.

---

**End of Part 9**

The next part will cover:

- **Security Operations (SecOps)**
- **Security Monitoring**
- **Audit Logging**
- **SIEM**
- **Threat Detection**
- **Incident Response**
- **Vulnerability Management**
- **Patch Management**
- **Penetration Testing**
- **Disaster Recovery from a Security Perspective**
