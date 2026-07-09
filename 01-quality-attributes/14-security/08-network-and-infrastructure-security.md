
# 54. Network Security

> **Network Security is the practice of protecting systems, services, and data as they communicate across networks by controlling traffic, isolating workloads, encrypting communication, and monitoring network activity.**

In modern architectures, network security is no longer just about protecting the perimeter.

It is about securing:

- Users
- APIs
- Microservices
- Databases
- Containers
- Cloud Networks
- Hybrid Environments

---

# Why Network Security Matters

Consider an E-Commerce System.

```text
Internet

↓

Load Balancer

↓

API Gateway

↓

Microservices

↓

Database
```

Without proper network security:

- Anyone can access services
- Databases may become publicly reachable
- Internal APIs may be abused
- Attackers can move laterally

---

# Network Security Objectives

Network security protects:

```text
Confidentiality

↓

Integrity

↓

Availability
```

While also enforcing:

- Segmentation
- Access Control
- Monitoring
- Isolation

---

# Network Security Layers

```text
Internet

↓

DDoS Protection

↓

Firewall

↓

WAF

↓

Load Balancer

↓

API Gateway

↓

Microservices

↓

Database Firewall

↓

Database
```

Every layer reduces risk.

---

# 55. Firewalls

> **A Firewall is a security device or software that monitors and controls network traffic based on predefined security rules.**

It acts as the first line of defense.

---

# Basic Firewall

```text
Internet

↓

Firewall

↓

Application
```

Firewall decides.

Allow?

Or.

Block?

---

# Firewall Rules

Examples.

Allow.

```
HTTPS

443
```

Block.

```
Telnet

23
```

---

# Packet Filtering Firewall

Examines.

- Source IP
- Destination IP
- Port
- Protocol

Decision made.

Per packet.

---

# Stateful Firewall

Unlike simple packet filters,

stateful firewalls understand connection state.

Example.

```text
Client

↓

HTTPS Request

↓

Firewall remembers session

↓

HTTPS Response

↓

Allowed
```

More secure.

Than stateless filtering.

---

# Next Generation Firewall (NGFW)

Modern firewalls provide:

- Deep Packet Inspection
- Application Awareness
- Intrusion Prevention (IPS)
- Malware Detection
- TLS Inspection
- Threat Intelligence

---

# Firewall Placement

Enterprise architecture.

```text
Internet

↓

Edge Firewall

↓

DMZ

↓

Internal Firewall

↓

Application Network

↓

Database Firewall

↓

Database
```

Multiple firewalls.

Support defense in depth.

---

# Firewall Best Practices

- Default deny
- Allow only required ports
- Review rules regularly
- Log denied traffic
- Remove unused rules

---

# 56. Web Application Firewall (WAF)

> **A Web Application Firewall protects HTTP/HTTPS applications from common web attacks by inspecting application-layer traffic.**

Unlike traditional firewalls,

a WAF understands:

- URLs
- Headers
- Cookies
- JSON
- HTTP methods

---

# Architecture

```text
Internet

↓

WAF

↓

API Gateway

↓

Application
```

---

# WAF Protects Against

- SQL Injection
- XSS
- Path Traversal
- Malicious Bots
- Protocol Violations
- Known Attack Signatures

---

# Example

Attacker sends.

```sql
' OR 1=1 --
```

WAF detects.

Blocks.

Request.

Before.

Application.

---

# WAF Limitations

WAF cannot replace:

- Secure coding
- Authorization
- Input validation

It complements them.

---

# 57. Demilitarized Zone (DMZ)

> **A DMZ is an isolated network segment that exposes public-facing services while protecting internal systems.**

---

# Architecture

```text
Internet

↓

Firewall

↓

DMZ

↓

Firewall

↓

Internal Network
```

---

# Typical DMZ Components

- Reverse Proxy
- Load Balancer
- API Gateway
- Public Web Server

Databases should never reside in the DMZ.

---

# Benefits

- Isolation
- Reduced blast radius
- Better monitoring
- Additional security layer

---

# 58. Virtual Private Cloud (VPC)

> **A VPC is a logically isolated virtual network within a cloud provider.**

Each organization controls:

- IP ranges
- Routing
- Firewalls
- Subnets
- Gateways

---

# Example

```text
VPC

├── Public Subnet
├── Private Subnet
├── Database Subnet
└── Management Subnet
```

Resources communicate securely.

Inside.

The VPC.

---

# Public vs Private Subnets

## Public Subnet

Accessible.

From Internet.

Examples.

- Load Balancer
- NAT Gateway
- Bastion Host

---

## Private Subnet

No direct Internet access.

Examples.

- Application Servers
- Microservices
- Databases
- Kafka
- Redis

Critical workloads should generally reside in private subnets.

---

# 59. Security Groups

> **Security Groups are stateful virtual firewalls attached directly to cloud resources.**

Example.

```text
Application Server

↓

Security Group

↓

Allow

443

↓

Database

3306
```

---

# Characteristics

- Stateful
- Resource level
- Allow rules
- Dynamic

---

# Example

Allow.

```
443

From

Load Balancer
```

Allow.

```
3306

Only.

Application Servers
```

---

# Best Practices

- Least privilege
- No public databases
- Restrict management ports
- Remove unused rules

---

# 60. Network ACL (NACL)

> **A Network ACL filters traffic entering and leaving an entire subnet.**

Unlike Security Groups,

NACLs are:

- Stateless
- Subnet level
- Allow and deny rules

---

# Security Group vs NACL

| Security Group | Network ACL |
|----------------|-------------|
| Stateful | Stateless |
| Resource Level | Subnet Level |
| Allow Rules | Allow & Deny Rules |
| Simpler | More Granular |

---

# Enterprise Usage

Use.

Security Groups.

For workload protection.

Use.

NACLs.

For subnet-level defense.

Together they provide layered security.

---

# 61. Virtual Private Network (VPN)

> **A VPN creates an encrypted tunnel between two trusted networks or devices over an untrusted network such as the Internet.**

---

# Example

Remote Employee.

```text
Laptop

↓

VPN

↓

Corporate Network
```

Traffic.

Encrypted.

---

# Site-to-Site VPN

```text
Data Center

↓

VPN Tunnel

↓

Cloud VPC
```

Frequently used during hybrid cloud migrations.

---

# Benefits

- Encryption
- Secure remote access
- Hybrid connectivity
- Cost-effective

---

# Limitations

- Increased latency
- Bandwidth constraints
- Operational management

---

# 62. Bastion Host (Jump Server)

> **A Bastion Host is a hardened server that provides controlled administrative access to private infrastructure.**

---

# Architecture

```text
Administrator

↓

SSH

↓

Bastion Host

↓

Private Servers
```

Administrators never connect directly to production servers.

---

# Best Practices

- MFA
- Logging
- Session recording
- Short-lived credentials
- Minimal installed software

---

# 63. Network Segmentation

> **Network Segmentation divides a network into isolated zones to reduce attack propagation.**

---

# Example

```text
Web Tier

↓

Application Tier

↓

Database Tier
```

Each tier.

Protected separately.

---

# Benefits

- Reduced lateral movement
- Better isolation
- Easier compliance
- Smaller blast radius

---

# Micro-Segmentation

Traditional segmentation protects networks.

Micro-segmentation protects workloads.

Example.

```text
Order Service

↓

Can Call

Inventory

↓

Cannot Call

HR Service
```

Policies become workload specific.

Widely implemented using service meshes and cloud-native networking.

---

# 64. Service Mesh Security

Modern microservices communicate constantly.

Security should be automatic.

---

# Service Mesh Provides

- mTLS
- Identity
- Authorization Policies
- Traffic Encryption
- Certificate Rotation
- Observability

---

# Example

```text
Order Service

⇄ mTLS ⇄

Inventory Service

⇄ mTLS ⇄

Payment Service
```

Developers do not manually implement TLS in every service.

The mesh enforces it.

---

# Service Identity

Every workload receives.

A unique identity.

Example.

```
spiffe://company/order-service
```

Authorization can then be based on service identity rather than IP addresses.

---

# 65. DDoS Protection

> **Distributed Denial of Service (DDoS) attacks overwhelm systems with enormous volumes of traffic, preventing legitimate users from accessing services.**

---

# Example

Legitimate traffic.

```
10,000 Requests/sec
```

Attacker.

```
20 Million Requests/sec
```

Infrastructure.

Becomes unavailable.

---

# Types of DDoS

```text
DDoS

├── Volumetric
├── Protocol
└── Application Layer
```

---

# Volumetric

Floods network bandwidth.

---

# Protocol Attack

Targets network protocols.

Consumes infrastructure resources.

---

# Application Layer

Targets expensive endpoints.

Example.

```
/search
```

Millions of requests.

---

# Protection

- CDN
- Global Load Balancers
- Rate Limiting
- WAF
- Auto Scaling
- Traffic Scrubbing
- Anycast Networks

---

# Defense Architecture

```text
Internet

↓

CDN

↓

DDoS Protection

↓

Firewall

↓

WAF

↓

Load Balancer

↓

API Gateway

↓

Application
```

Multiple layers.

Provide resilience.

---

# East-West vs North-South Security

## North-South

Traffic entering.

Or leaving.

Organization.

```text
Internet

↓

Gateway

↓

Services
```

Focus.

- WAF
- API Gateway
- Firewalls
- DDoS Protection

---

## East-West

Traffic.

Between services.

```text
Order

↓

Inventory

↓

Payment
```

Focus.

- mTLS
- Service Mesh
- Authorization Policies
- Micro-Segmentation

---

# Zero Trust Networking

Traditional networking.

Trusted.

Internal traffic.

---

Zero Trust.

Requires verification.

For every connection.

Regardless of location.

---

# Common Mistakes

### Public Databases

Databases should not be directly accessible from the Internet.

Place them in private networks.

---

### Flat Networks

If every server can communicate with every other server,

attackers can move laterally with ease.

Segment networks.

---

### Opening All Ports

Rules such as:

```
0.0.0.0/0

All Ports
```

Are rarely appropriate for production.

Apply least privilege.

---

### Relying Only on Firewalls

Firewalls alone cannot stop application-layer attacks.

Combine:

- WAF
- Authentication
- Authorization
- Secure coding
- Monitoring

---

### Ignoring Internal Traffic

Many breaches spread through internal networks after the initial compromise.

Protect East-West traffic with Zero Trust principles.

---

# Architect's Perspective

Network Security has evolved dramatically.

The old model assumed:

> **"Protect the perimeter and trust everything inside."**

Modern enterprise architecture assumes:

> **"Every network is potentially hostile."**

Architects therefore design systems where:

- Networks are segmented.
- Services authenticate each other.
- Communication is encrypted.
- Workloads have verifiable identities.
- Firewalls enforce least privilege.
- Public exposure is minimized.
- Monitoring continuously detects abnormal traffic.

The goal is not merely to block attackers from entering,

but to ensure that **even if an attacker gains access to one component, they cannot easily move throughout the system or compromise critical business assets.**

---

**End of Part 7**

The next part will cover:

- **Cloud Security**
- **Shared Responsibility Model**
- **IAM**
- **Secure Storage**
- **Container Security**
- **Kubernetes Security**
- **Image Scanning**
- **Runtime Security**
- **Cloud Security Best Practices**
