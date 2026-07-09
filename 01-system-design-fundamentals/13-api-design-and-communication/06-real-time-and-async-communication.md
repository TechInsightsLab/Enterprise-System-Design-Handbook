
# 22. WebSockets

> **WebSockets provide a persistent, full-duplex communication channel between a client and a server, allowing both sides to send messages at any time.**

Unlike REST,

where every request requires a new HTTP request,

WebSocket establishes:

One persistent connection.

---

# Why WebSockets?

Imagine building:

- Chat Application
- Stock Trading Platform
- Multiplayer Game
- Live Sports Dashboard

Using REST.

Client must repeatedly ask:

```text
Any new messages?

↓

No

↓

Any new messages?

↓

No

↓

Any new messages?
```

This is called:

Polling.

Inefficient.

---

# WebSocket Solution

Create connection once.

```text
Client

⇄

Server
```

Both communicate freely.

No repeated HTTP requests.

---

# Connection Lifecycle

```text
HTTP Request

↓

Upgrade

↓

WebSocket Connection

↓

Bi-directional Communication

↓

Close Connection
```

Initially.

WebSocket starts as HTTP.

Then upgrades.

---

# Architecture

```text
Client

⇄

WebSocket Server

⇄

Application

⇄

Database
```

---

# Full Duplex Communication

REST

```text
Client

↓

Server

↓

Response
```

---

WebSocket

```text
Client

⇄

Server
```

Both can initiate communication.

---

# Example

Customer Support Chat.

Customer sends.

```
Hello
```

Agent replies.

```
How can I help?
```

No polling.

Instant communication.

---

# Enterprise Use Cases

- Chat
- Online Gaming
- Financial Trading
- Live Dashboards
- IoT Monitoring
- Collaborative Editing
- Real-Time Notifications

---

# Advantages

- Low latency
- Full duplex
- Persistent connection
- Reduced network overhead
- Real-time communication

---

# Challenges

- Connection management
- Scaling
- Stateful connections
- Load balancing complexity
- Idle connection handling

---

# Scaling WebSockets

One server.

```
100,000

Connections
```

Need more?

Multiple servers.

---

Architecture.

```text
Clients

↓

Load Balancer

↓

WebSocket Servers

↓

Redis Pub/Sub

↓

Application
```

Servers synchronize.

Messages.

Through shared infrastructure.

---

# Sticky Sessions

Because connections remain open,

requests often need to return to the same server unless a shared connection management strategy is implemented.

Sticky sessions are one possible approach.

---

# Heartbeats

Persistent connections.

Can silently disconnect.

Servers periodically send.

```
PING
```

Clients reply.

```
PONG
```

Dead connections removed.

---

# 23. Server-Sent Events (SSE)

> **Server-Sent Events allow the server to continuously push updates to the client over a single HTTP connection.**

Unlike WebSocket,

communication is:

One-way.

---

# Architecture

```text
Server

↓

Client
```

Server pushes.

Client receives.

---

# Example

Live News Feed.

Server publishes.

```
Breaking News
```

Browser updates automatically.

---

# Connection

```text
Client

↓

HTTP Request

↓

Persistent Connection

↓

Server Streams Events
```

---

# Characteristics

- One-way communication
- HTTP based
- Automatic reconnect
- Simple implementation

---

# Enterprise Use Cases

- Live dashboards
- Notification feeds
- Monitoring
- Stock prices
- Sports scores
- Build pipelines

---

# Advantages

- Simple
- Lightweight
- Automatic reconnect
- HTTP compatible

---

# Limitations

Client cannot push data through the same connection.

If two-way communication is required,

WebSocket is a better choice.

---

# SSE Example

```text
Build Started

↓

20%

↓

45%

↓

80%

↓

Completed
```

Browser updates continuously.

---

# 24. Webhooks

> **A Webhook is an HTTP callback where one system automatically notifies another system when an event occurs.**

Unlike polling,

the receiving system waits.

The sender initiates communication.

---

# Traditional Polling

```text
Client

↓

Any Updates?

↓

No

↓

Any Updates?

↓

No

↓

Any Updates?
```

Many unnecessary requests.

---

# Webhook

```text
Payment Completed

↓

Webhook

↓

Merchant
```

Only when an event occurs.

---

# Real-World Example

Customer pays using Stripe.

Instead of merchant asking repeatedly:

```
Payment Complete?
```

Stripe sends:

```http
POST /payment-webhook
```

Immediately after payment succeeds.

---

# Architecture

```text
Application A

↓

Event

↓

Webhook

↓

Application B
```

---

# Enterprise Use Cases

- Payment gateways
- GitHub events
- CI/CD pipelines
- CRM integrations
- ERP integrations
- Shipping notifications

---

# Advantages

- Near real-time
- Efficient
- Event-driven
- Simple integration

---

# Challenges

- Retry handling
- Authentication
- Signature verification
- Duplicate events
- Idempotency

---

# Webhook Security

Never trust incoming webhook requests.

Verify:

- Signature
- Timestamp
- Secret
- Source

Many providers use HMAC signatures to verify authenticity.

---

# 25. Async APIs

> **Async APIs enable communication through events instead of synchronous request-response interactions.**

Unlike REST.

Client.

Does not wait.

---

# Architecture

```text
Producer

↓

Kafka

↓

Consumer
```

Request.

And processing.

Decoupled.

---

# Example

Customer uploads.

Large video.

REST returns.

```
202 Accepted
```

Video encoding happens asynchronously.

Completion notification arrives later.

---

# Async Communication Flow

```text
Upload Request

↓

API

↓

Kafka

↓

Encoding Service

↓

Notification
```

---

# Enterprise Uses

- Order Processing
- Payments
- Notifications
- Video Processing
- Report Generation
- Machine Learning

---

# Benefits

- Loose coupling
- Better scalability
- Higher throughput
- Improved resilience

---

# Challenges

- Eventual consistency
- Monitoring complexity
- Debugging
- Replay
- Message ordering

---

# AsyncAPI Specification

Just as OpenAPI documents REST,

**AsyncAPI** documents event-driven APIs.

It describes:

- Channels
- Messages
- Schemas
- Publishers
- Subscribers
- Security

---

# REST vs Async API

| REST | Async API |
|--------|------------|
| Request-Response | Event Driven |
| Immediate response | Processing later |
| Synchronous | Asynchronous |
| Client waits | Client continues |

---

# 26. Real-Time Communication Patterns

Modern systems have multiple choices.

```text
REST

↓

Polling

↓

Long Polling

↓

SSE

↓

WebSocket

↓

Messaging
```

Each serves different needs.

---

# Polling

Client repeatedly requests.

Updates.

Simple.

But inefficient.

---

# Long Polling

Client waits.

Server responds.

When data available.

Then reconnects.

Better than polling.

Still incurs repeated HTTP requests.

---

# SSE

Persistent.

Server pushes.

Client receives.

One direction.

---

# WebSocket

Persistent.

Both directions.

---

# Messaging

Event-driven.

Usually not directly connected to browsers.

Ideal for backend workflows.

---

# Real-Time Comparison

| Technology | Client → Server | Server → Client | Persistent |
|------------|-----------------|-----------------|------------|
| REST | Request | Response | No |
| Polling | Yes | Yes | No |
| Long Polling | Yes | Yes | Temporary |
| SSE | Limited (new HTTP request required) | Yes | Yes |
| WebSocket | Yes | Yes | Yes |
| Messaging | Through broker | Through broker | Depends on implementation |

---

# Choosing the Right Technology

## CRUD Application

Use.

REST.

---

## Chat Application

Use.

WebSocket.

---

## Live Dashboard

Use.

SSE.

---

## Event Notification

Use.

Webhook.

---

## Long Running Workflow

Use.

Async Messaging.

---

## Internal Service Communication

Use.

gRPC.

---

# Technology Selection Matrix

| Scenario | Best Choice |
|-----------|-------------|
| CRUD APIs | REST |
| Mobile Backend | GraphQL |
| Microservices | gRPC |
| Chat | WebSocket |
| Live Notifications | SSE |
| Third-Party Event Callback | Webhook |
| Background Processing | Async API |
| Event Streaming | Kafka |

---

# Hybrid Enterprise Architecture

Large enterprises.

Rarely use.

One communication style.

Example.

```text
Browser

↓

REST

↓

API Gateway

↓

GraphQL

↓

Microservices

↓

gRPC

↓

Kafka

↓

Analytics

↓

WebSocket

↓

Browser Notifications
```

Every technology.

Has its role.

---

# Common Mistakes

### Using WebSocket for Simple CRUD

Persistent connections increase operational complexity.

REST is usually sufficient.

---

### Polling Every Second

Creates unnecessary network traffic.

Use WebSocket or SSE where appropriate.

---

### Ignoring Webhook Retries

Webhook providers retry failed deliveries.

Endpoints must be idempotent.

---

### Using Async APIs for Immediate User Feedback

Some operations require synchronous confirmation.

Choose asynchronous communication only when business workflows permit delayed processing.

---

### Forgetting Connection Management

WebSockets require monitoring for:

- Heartbeats
- Idle timeouts
- Reconnects
- Connection limits

---

# Architect's Perspective

Communication technology should match the **interaction pattern**, not personal preference.

Ask these questions:

- Does the client need an immediate response?
- Is communication one-way or two-way?
- Is the workload real-time?
- Will millions of clients stay connected?
- Is the communication browser-based or service-to-service?
- Can the business tolerate eventual consistency?

A modern enterprise platform typically combines:

- **REST** for standard business APIs
- **GraphQL** for flexible client data access
- **gRPC** for internal service communication
- **WebSockets** for bidirectional real-time interaction
- **SSE** for server-driven updates
- **Webhooks** for third-party integrations
- **Async APIs** for long-running business workflows

Choosing the right communication model is often more important than choosing the underlying technology.

---

**End of Part 4A**

The next part will cover:

- **API Security**
- **Authentication**
- **Authorization**
- **API Keys**
- **OAuth 2.0**
- **OpenID Connect (OIDC)**
- **JWT**
- **Mutual TLS (mTLS)**
- **CORS**
- **CSRF**
- **Input Validation**
- **Rate Limiting & Throttling**
