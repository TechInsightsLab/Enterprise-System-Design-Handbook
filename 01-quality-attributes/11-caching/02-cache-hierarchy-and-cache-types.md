
# 1. Cache Hierarchy

> **Caching is not a single technology. It is a hierarchy of progressively larger, slower, and cheaper storage layers that work together to minimize data access time.**

Most enterprise systems use **multiple cache layers simultaneously**.

Every layer serves a different purpose.

---

# Complete Cache Hierarchy

```text
                    Fastest
                       ▲
                       │
                CPU Registers
                       │
                    L1 Cache
                       │
                    L2 Cache
                       │
                    L3 Cache
                       │
                     RAM
                       │
              OS Page Cache
                       │
            Application Cache
                       │
            Distributed Cache
                       │
             Reverse Proxy Cache
                       │
                 CDN Cache
                       │
                Database Cache
                       │
                  Database
                       │
                Object Storage
                       │
                     Disk
                       ▼
                    Slowest
```

Every layer attempts to answer the request before it reaches the next slower layer.

---

# Why Multiple Cache Layers?

Imagine a request for:

```
Product Details
```

Instead of immediately querying the database,

the request checks multiple cache levels.

```text
Browser Cache

↓

CDN

↓

Application Cache

↓

Redis

↓

Database
```

If the Browser already has the response,

nothing else is contacted.

---

# Principle

The closer data is to the consumer,

the lower the latency.

---

# Enterprise Example

Netflix.

Movie thumbnail.

```text
Browser

↓

CDN

↓

Application

↓

Redis

↓

Metadata Database

↓

S3
```

Most requests never reach S3.

---

# Latency Comparison

Approximate latency comparison:

| Layer | Approximate Latency |
|---------|--------------------|
| CPU Register | <1 ns |
| L1 Cache | ~1 ns |
| L2 Cache | ~3–5 ns |
| L3 Cache | ~10–20 ns |
| RAM | ~50–150 ns |
| Redis | ~0.2–1 ms |
| Database | ~5–50 ms |
| Remote API | 50–500+ ms |

Notice:

Every level becomes:

- Slower
- Larger
- Cheaper

---

# Memory Pyramid

```text
Tiny

↓

Extremely Fast

↓

Expensive

--------------------

Huge

↓

Slow

↓

Cheap
```

Architects continuously balance:

- Speed
- Cost
- Capacity

---

# 2. CPU Cache

> **CPU Cache is high-speed memory located inside the processor that stores frequently accessed instructions and data.**

Every modern CPU contains multiple cache levels.

Applications use them automatically.

No code changes required.

---

# CPU Cache Levels

```text
CPU

├── L1

├── L2

└── L3
```

---

# L1 Cache

Closest to CPU.

Characteristics:

- Smallest
- Fastest
- Usually dedicated per core

---

Typical size:

```
32 KB

64 KB
```

---

# L2 Cache

Larger.

Slightly slower.

Usually dedicated to a core.

---

Typical size:

```
256 KB

1 MB
```

---

# L3 Cache

Shared among CPU cores.

Much larger.

Higher latency.

---

Typical size:

```
8 MB

64 MB
```

or more depending on processor.

---

# Why CPU Cache Exists

Suppose CPU executes:

```java
sum += numbers[i];
```

Millions of times.

Without cache:

CPU repeatedly reads RAM.

Very expensive.

Instead:

Frequently used data remains:

Inside CPU cache.

---

# CPU Cache Locality

Performance improves when applications access nearby memory.

Two important concepts:

- Temporal Locality
- Spatial Locality

---

# Temporal Locality

Recently accessed data is likely to be accessed again.

Example:

Loop variable.

---

# Spatial Locality

Nearby memory locations are likely to be accessed together.

Example:

Arrays.

---

# Why Architects Should Know CPU Cache

Although application developers don't control CPU caches directly,

software design influences cache efficiency.

Examples:

- Sequential memory access
- Efficient data structures
- Avoiding excessive pointer chasing

These concepts become important in:

- High-frequency trading
- Databases
- Search engines
- Game engines

---

# 3. Operating System Page Cache

> **The Operating System caches frequently accessed disk pages in RAM to reduce expensive disk I/O.**

Applications may believe they are reading:

Disk.

Actually,

the operating system serves:

Memory.

---

# Architecture

```text
Application

↓

OS Page Cache

↓

Disk
```

---

# Example

Read:

```
customer.csv
```

First read:

Disk accessed.

Second read:

Served from RAM.

Much faster.

---

# Benefits

- Reduced disk reads
- Better filesystem performance
- Faster application startup

---

# Common Misconception

Many developers assume:

```
Read File

↓

Disk
```

Actually:

```
Read File

↓

OS Cache

↓

Maybe Disk
```

---

# Database Interaction

Databases often interact carefully with the OS page cache.

Some rely heavily on it.

Others manage their own buffer pools to avoid double caching.

Understanding this behavior helps architects size memory correctly.

---

# 4. Browser Cache

> **Browser Cache stores web resources locally on the user's device to avoid repeated downloads.**

Examples:

- Images
- CSS
- JavaScript
- Fonts
- Logos

---

# Example

First visit:

```text
Browser

↓

Internet

↓

Download Logo
```

Second visit:

```text
Browser

↓

Local Cache
```

No network request.

---

# Benefits

- Faster page load
- Reduced bandwidth
- Better user experience

---

# Typical Resources

Cache:

- CSS
- JS
- Images
- Fonts
- Static HTML
- Icons

---

# Cache-Control Header

Servers control browser caching using HTTP headers.

Example:

```http
Cache-Control:

max-age=3600
```

Meaning:

Cache resource for:

```
1 Hour
```

---

# Browser Validation

Instead of downloading again,

browser asks:

```
Has This File Changed?
```

Using:

- ETag
- Last-Modified

If unchanged,

server responds:

```
304 Not Modified
```

Tiny response.

Huge bandwidth savings.

---

# Benefits

- Lower bandwidth
- Faster reloads
- Better scalability

---

# Cache Busting

Problem:

User receives:

Old JavaScript.

Solution:

Version resources.

Example:

```text
app.js?v=15
```

or

```text
app.a6d93f.js
```

New filename.

Browser downloads fresh copy.

---

# 5. HTTP Cache

HTTP caching is governed by standard response headers.

Common headers include:

| Header | Purpose |
|---------|----------|
| Cache-Control | Defines caching rules |
| Expires | Absolute expiration time |
| ETag | Resource version identifier |
| Last-Modified | Last update timestamp |
| Age | How long response has been cached |

---

# Cache-Control Directives

Examples:

```
public
```

Shared caches may store.

---

```
private
```

Only browser caches.

---

```
no-cache
```

Must revalidate before reuse.

---

```
no-store
```

Do not cache at all.

---

```
max-age=600
```

Valid for:

```
10 Minutes
```

---

# Example

Product image.

```http
Cache-Control:

public,

max-age=86400
```

Cached for:

```
24 Hours
```

---

# 6. Reverse Proxy Cache

A reverse proxy sits between:

Users.

Applications.

It can cache responses.

---

Architecture:

```text
Client

↓

Reverse Proxy

↓

Application
```

---

Popular Technologies

- NGINX
- Varnish
- HAProxy
- Apache Traffic Server

---

# Example

Homepage.

```text
100,000 Requests

↓

Reverse Proxy

↓

One Backend Request
```

Massive reduction.

---

# Best Use Cases

- Static HTML
- Product Pages
- API Responses
- Images

---

# Benefits

- Reduced backend load
- Better scalability
- Faster response

---

# Challenges

Need:

Cache invalidation.

When product changes,

cached page must update.

---

# 7. Content Delivery Network (CDN)

> **A CDN is a globally distributed network of edge servers that cache content closer to end users.**

Instead of every user contacting:

One datacenter.

Users contact:

Nearest edge location.

---

# Architecture

```text
User

↓

Nearest CDN Edge

↓

Origin Server
```

---

# Example

Website hosted:

```
Mumbai
```

User:

Brazil.

Without CDN:

Brazil → Mumbai.

---

With CDN:

Brazil → São Paulo Edge.

Much faster.

---

# What CDNs Cache

- Images
- Videos
- JavaScript
- CSS
- PDFs
- Static files
- API responses (when appropriate)

---

# Benefits

- Lower latency
- Reduced origin traffic
- Better availability
- DDoS protection
- Lower bandwidth cost

---

# Popular CDNs

| Provider | Service |
|-----------|----------|
| AWS | CloudFront |
| Azure | Azure CDN |
| Google Cloud | Cloud CDN |
| Cloudflare | CDN + Security |
| Akamai | Enterprise CDN |
| Fastly | Edge Platform |

---

# Dynamic Content

Modern CDNs can also cache selected API responses.

Example:

```
GET

/products
```

Frequently accessed.

Low update frequency.

Excellent CDN candidate.

---

# CDN Cache Miss

Workflow.

```text
User

↓

CDN

↓

Origin

↓

CDN Stores Response

↓

User
```

Future users receive cached content.

---

# Multi-Level Request Flow

Real enterprise request.

```text
Browser Cache

↓

CDN

↓

Reverse Proxy

↓

Application Cache

↓

Redis

↓

Database
```

Every layer reduces load on the next.

---

# Common Mistakes

### Caching Everything

Dynamic data should not automatically be cached.

Evaluate freshness requirements.

---

### Ignoring Browser Cache

Applications repeatedly download identical JavaScript and CSS.

Unnecessary bandwidth.

---

### Poor Cache-Control Headers

Missing cache directives cause browsers and CDNs to bypass caching.

---

### Forgetting Cache Busting

Users continue running outdated application code after deployments.

---

# Architect's Perspective

Caching is **hierarchical**.

Experienced architects do not ask:

> **"Should we add Redis?"**

Instead, they ask:

- Can the browser cache it?
- Can the CDN cache it?
- Can the reverse proxy cache it?
- Can the application cache it?
- Does it even need Redis?
- Should the database buffer pool already handle it?

The fastest request is the one that never reaches the backend.

Every higher cache layer eliminates work for every lower layer.

A well-designed caching hierarchy dramatically improves:

- Latency
- Scalability
- Availability
- Infrastructure cost

without modifying core business logic.

---

**End of Part 2**

The next part will cover:

- **Application Cache**
- **Database Cache**
- **Distributed Cache**
- **Object Cache**
- **Session Cache**
- **Query Cache**
- **Metadata Cache**
- **Configuration Cache**
- **Near Cache**
- **Multi-Level Application Caching**
