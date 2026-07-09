
# 22. Cryptography Fundamentals

> **Cryptography is the science of protecting information by transforming it into a form that unauthorized parties cannot understand or modify.**

Cryptography is one of the most fundamental building blocks of modern security.

Every day you use cryptography when:

- Logging into banking applications
- Making online payments
- Sending WhatsApp messages
- Using HTTPS websites
- Accessing cloud services
- Connecting microservices
- Signing software packages

Without cryptography,

modern distributed systems would not exist.

---

# Why Cryptography?

Imagine sending your ATM PIN.

Without encryption.

```text
ATM PIN

1234
```

Anyone intercepting the network can read it.

Now.

Encrypt it.

```text
7F3A91B8D...
```

Without the encryption key,

the data is useless.

---

# Security Goals of Cryptography

Cryptography supports multiple security objectives.

```text
Confidentiality

↓

Integrity

↓

Authentication

↓

Non-Repudiation
```

Notice.

Cryptography supports much more than encryption.

---

# Cryptography Overview

```text
Cryptography

├── Encryption
├── Decryption
├── Hashing
├── Digital Signatures
├── Certificates
├── Key Management
└── Secure Randomness
```

Each solves a different problem.

---

# Plaintext vs Ciphertext

Before encryption.

```text
Hello Customer
```

Plaintext.

After encryption.

```text
D82AB901A3F...

```

Ciphertext.

Only authorized parties can decrypt it.

---

# Basic Encryption Flow

```text
Plaintext

↓

Encryption Algorithm

+

Key

↓

Ciphertext

↓

Transmission

↓

Decryption

↓

Plaintext
```

---

# Algorithms vs Keys

A common misunderstanding.

Security comes primarily from:

The **key**.

Not from hiding the algorithm.

Modern algorithms are public.

Keys remain secret.

This follows **Kerckhoffs's Principle**.

---

# Kerckhoffs's Principle

> **A cryptographic system should remain secure even if everything about the system is public except the secret key.**

Never rely on:

"Hiding the algorithm."

Instead.

Protect the key.

---

# 23. Symmetric Encryption

> **Symmetric Encryption uses the same secret key for both encryption and decryption.**

---

# Workflow

```text
Plaintext

↓

Secret Key

↓

Encryption

↓

Ciphertext

↓

Secret Key

↓

Decryption
```

Same key.

Both directions.

---

# Example

Alice.

And.

Bob.

Share.

One secret key.

```text
Alice

⇄ Secret Key ⇄

Bob
```

---

# Advantages

- Extremely fast
- Efficient
- Suitable for large files
- Low CPU usage

---

# Disadvantages

- Key distribution problem
- Large environments require many keys
- Difficult key management

---

# Common Algorithms

Modern symmetric algorithms include:

- AES (Advanced Encryption Standard)
- ChaCha20

Legacy algorithms such as DES and 3DES are considered obsolete and should not be used for new systems.

---

# Enterprise Uses

Symmetric encryption protects:

- Database storage
- File encryption
- Backup encryption
- Disk encryption
- Object storage
- Network traffic (after session establishment)

---

# AES

AES is the industry standard.

Supports:

```
AES-128

AES-192

AES-256
```

Most enterprise systems use AES.

---

# Example

Cloud Storage.

```text
Upload File

↓

AES Encrypt

↓

Store
```

File remains encrypted.

At rest.

---

# 24. Asymmetric Encryption

> **Asymmetric Encryption uses two mathematically related keys: a public key and a private key.**

---

# Key Pair

```text
Public Key

↓

Encrypt

-----------------

Private Key

↓

Decrypt
```

Different keys.

Different purposes.

---

# Public Key

Can be shared.

With anyone.

---

# Private Key

Must never be shared.

Only owner possesses it.

---

# Example

Alice publishes.

Public Key.

Anyone can encrypt.

Only Alice.

Can decrypt.

Using.

Private Key.

---

# Workflow

```text
Public Key

↓

Encryption

↓

Ciphertext

↓

Private Key

↓

Decryption
```

---

# Advantages

- Solves key distribution problem
- Enables digital signatures
- Enables PKI
- Enables TLS

---

# Disadvantages

- Slower than symmetric encryption
- Higher CPU usage
- Not ideal for encrypting very large datasets directly

---

# Common Algorithms

Widely used algorithms include:

- RSA
- Elliptic Curve Cryptography (ECC)

Modern systems increasingly prefer ECC for many use cases because it provides strong security with smaller key sizes.

---

# Why Use Both?

Modern HTTPS.

Uses.

Both.

```text
Asymmetric

↓

Secure Key Exchange

↓

Symmetric

↓

Data Encryption
```

Best of both worlds.

---

# Symmetric vs Asymmetric

| Symmetric | Asymmetric |
|------------|------------|
| Same key | Public + Private Keys |
| Very Fast | Slower |
| Large Data | Key Exchange |
| AES | RSA / ECC |

---

# 25. Hashing

> **Hashing converts data into a fixed-length value that cannot be reversed.**

Hashing is **not encryption**.

You cannot decrypt.

A hash.

---

# Example

Input.

```
Password123
```

Hash.

```
5e88489...
```

Always.

Fixed length.

---

# Properties

Good hash functions are:

- Deterministic
- Fast to compute
- One-way
- Collision resistant
- Avalanche effect

---

# Avalanche Effect

Small change.

Input.

```
Password123
```

↓

```
Password124
```

Produces.

Completely different.

Hash.

---

# Common Hash Algorithms

Modern secure hashing includes:

- SHA-256
- SHA-384
- SHA-512

Algorithms such as MD5 and SHA-1 are no longer considered secure for collision-resistant applications.

---

# Enterprise Uses

Hashing used for:

- Password verification (with dedicated password hashing algorithms)
- File integrity
- Digital signatures
- Blockchain
- Checksums

---

# Password Hashing

Passwords should not use general-purpose hashes directly.

Instead use dedicated password hashing algorithms such as:

- Argon2
- bcrypt
- scrypt
- PBKDF2

These are intentionally computationally expensive to resist brute-force attacks.

---

# Salt

Before hashing.

Generate.

Random salt.

```text
Password

+

Salt

↓

Hash
```

Salt prevents identical passwords from producing identical hashes.

---

# Example

User A.

Password.

```
Welcome123
```

Salt.

```
XYZ
```

Hash differs.

From.

User B.

Using same password.

---

# 26. Digital Signatures

> **Digital Signatures verify the authenticity, integrity, and origin of data.**

Digital signatures do **not** primarily provide confidentiality.

They prove.

Who signed.

And whether data changed.

---

# Workflow

```text
Document

↓

Hash

↓

Private Key

↓

Signature
```

Recipient.

Uses.

Public Key.

To verify.

---

# Verification

```text
Document

↓

Hash

↓

Public Key

↓

Valid?

↓

Trusted
```

---

# Enterprise Uses

Digital signatures protect:

- Software packages
- API requests
- Contracts
- Documents
- Certificates
- Banking transactions

---

# Benefits

- Integrity
- Authentication
- Non-repudiation

---

# Non-Repudiation

> **The sender cannot reasonably deny having signed the data.**

Because.

Only the sender possesses.

Private key.

---

# 27. Public Key Infrastructure (PKI)

> **PKI is the system that manages digital certificates and public keys.**

PKI establishes.

Trust.

Across.

Large organizations.

And.

The Internet.

---

# Components

```text
PKI

├── Certificate Authority (CA)
├── Registration Authority (RA)
├── Certificates
├── Public Keys
├── Private Keys
└── Revocation Services
```

---

# Certificate Authority (CA)

Trusted organization.

Issues.

Digital certificates.

Examples.

- DigiCert
- Let's Encrypt
- Internal Enterprise CA

---

# Certificate

A certificate binds.

Identity.

To.

Public Key.

Contains.

- Subject
- Issuer
- Public Key
- Validity
- Signature

---

# Certificate Chain

```text
Root CA

↓

Intermediate CA

↓

Server Certificate
```

Trust flows.

Down the chain.

---

# Certificate Revocation

Certificates may be revoked.

If:

- Private key compromised
- Organization breached
- Certificate issued incorrectly

Systems check revocation status before trusting certificates.

---

# 28. TLS (Transport Layer Security)

> **TLS protects data while it is transmitted across a network.**

Whenever you see:

```
https://
```

TLS is involved.

---

# TLS Protects

- Confidentiality
- Integrity
- Authentication

During communication.

---

# TLS Handshake (Simplified)

```text
Client

↓

Hello

↓

Server Certificate

↓

Verify Certificate

↓

Exchange Session Key

↓

Encrypted Communication
```

After the handshake.

Communication uses efficient symmetric encryption.

---

# Why HTTPS?

HTTP.

```text
Readable
```

HTTPS.

```text
Encrypted
```

---

# TLS Example

Browser.

↓

Bank.

↓

Encrypted.

Nobody on the network can read the traffic.

---

# 29. Mutual TLS (mTLS)

Standard TLS.

Authenticates.

Server.

Only.

---

mTLS.

Authenticates.

Both.

Client.

And.

Server.

---

# Workflow

```text
Client Certificate

↓

Server Certificate

↓

Mutual Verification

↓

Encrypted Connection
```

---

# Enterprise Uses

- Banking
- Healthcare
- Government
- Microservices
- Service Mesh

---

# Benefits

- Strong machine identity
- No passwords
- Excellent for service-to-service communication

---

# 30. Key Management

> **Strong cryptography is ineffective if encryption keys are poorly managed.**

Keys require lifecycle management.

---

# Key Lifecycle

```text
Generate

↓

Store

↓

Use

↓

Rotate

↓

Revoke

↓

Destroy
```

---

# Key Rotation

Never use.

One encryption key.

Forever.

Rotate.

Regularly.

Especially after:

- Compromise
- Employee departure
- Policy requirement

---

# Key Storage

Never hardcode keys.

```java
String SECRET="password123";
```

Instead use.

- Secrets Manager
- Vault
- HSM
- KMS

---

# 31. Hardware Security Module (HSM)

> **An HSM is a tamper-resistant hardware device designed to generate, store, and use cryptographic keys securely.**

Private keys.

Never leave.

The HSM.

---

# Enterprise Uses

- Banking
- Payment systems
- Certificate Authorities
- Government
- PKI

---

# Benefits

- Strong physical security
- Secure key generation
- Compliance
- Tamper resistance

---

# 32. Secrets Management

Secrets include:

- API Keys
- Database Passwords
- OAuth Secrets
- JWT Signing Keys
- TLS Private Keys
- Cloud Credentials

---

# Bad Practice

```text
Git Repository

↓

Database Password
```

Never store secrets.

In source code.

---

# Good Practice

```text
Application

↓

Secrets Manager

↓

Runtime Secret
```

---

# Popular Solutions

- HashiCorp Vault
- AWS Secrets Manager
- Azure Key Vault
- Google Secret Manager
- Kubernetes Secrets (often combined with external secret management)

---

# Secret Rotation

Secrets should be:

- Rotated regularly
- Audited
- Access controlled
- Versioned where supported

Automated rotation reduces operational risk.

---

# Encryption at Rest vs Encryption in Transit

| At Rest | In Transit |
|-----------|------------|
| Database | HTTPS |
| Disk | TLS |
| Object Storage | mTLS |
| Backups | VPN/TLS |

Use both.

Not one.

---

# Common Mistakes

### Hardcoding Secrets

Never commit credentials.

To source code.

---

### Using One Encryption Key Forever

Rotate keys.

Regularly.

---

### Using MD5 for Passwords

Use modern password hashing algorithms.

Never MD5.

---

### Treating Hashing as Encryption

Hashing.

Cannot be reversed.

Encryption.

Can.

---

### Ignoring Certificate Expiration

Expired certificates.

Can bring production systems offline.

Monitor and renew certificates proactively.

---

# Architect's Perspective

Cryptography is not about selecting algorithms.

It is about **using the right cryptographic primitive for the right problem**.

Ask:

- Do I need confidentiality? → Encryption.
- Do I need integrity? → Hashing or Digital Signatures.
- Do I need authentication? → Certificates, Digital Signatures, TLS.
- Do I need non-repudiation? → Digital Signatures.
- Do I need secure machine-to-machine communication? → mTLS.
- Do I need long-term protection? → Strong key management and rotation.

The strongest encryption algorithm cannot protect a system if:

- Keys are exposed.
- Secrets are committed to source code.
- Certificates are unmanaged.
- Private keys are leaked.

In enterprise security, **key management is often more important than the encryption algorithm itself**.

---

**End of Part 4**

The next part will cover:

- **Application Security**
- **Secure Coding**
- **OWASP Top 10**
- **SQL Injection**
- **NoSQL Injection**
- **Command Injection**
- **Cross-Site Scripting (XSS)**
- **CSRF**
- **SSRF**
- **XXE**
- **Path Traversal**
- **File Upload Security**
- **Deserialization Attacks**
