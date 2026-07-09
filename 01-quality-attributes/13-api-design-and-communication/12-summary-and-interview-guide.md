
# 80. Interview Preparation

This section contains interview questions frequently asked for:

- Software Engineer
- Senior Engineer
- Technical Lead
- Solution Architect
- Principal Architect

The goal is to evaluate **architectural thinking**, not memorization.

---

# Beginner Level

## 1. What is an API?

**Expected Answer**

An API (Application Programming Interface) is a contract that enables different software systems to communicate without exposing internal implementation details.

---

## 2. Why do we need APIs?

Expected discussion:

- Standardized communication
- Loose coupling
- Reusability
- Security
- Integration
- Scalability

---

## 3. What is REST?

REST is an architectural style that models business entities as resources and manipulates them using standard HTTP methods.

---

## 4. What is a Resource?

A resource represents a business entity exposed through an API.

Examples:

- Customer
- Product
- Order

---

## 5. What are HTTP Methods?

Expected discussion:

- GET
- POST
- PUT
- PATCH
- DELETE

Explain when each should be used.

---

## 6. Difference Between PUT and PATCH?

PUT replaces the entire resource.

PATCH updates only selected fields.

---

## 7. What is Idempotency?

Repeated execution of the same operation produces the same final state.

---

## 8. What is API Versioning?

Versioning allows APIs to evolve without breaking existing consumers.

---

## 9. What is JSON?

JSON is the most commonly used lightweight data exchange format for REST APIs.

---

## 10. Why Should APIs Return Proper HTTP Status Codes?

Status codes communicate the result of a request using standard HTTP semantics, enabling clients, proxies, and monitoring tools to behave correctly.

---

# Intermediate Level

## 11. Explain REST Constraints.

Discuss:

- Client-Server
- Stateless
- Cacheable
- Uniform Interface
- Layered System
- Code-on-Demand

---

## 12. Explain Richardson Maturity Model.

Levels:

- Level 0
- Level 1
- Level 2
- Level 3

---

## 13. Difference Between 401 and 403?

401:

Authentication required.

403:

Authenticated.

Access denied.

---

## 14. Explain Pagination.

Expected discussion:

- Offset Pagination
- Cursor Pagination
- Keyset Pagination

Trade-offs.

---

## 15. What is HATEOAS?

Hypermedia allows clients to discover available actions through links included in responses.

---

## 16. Explain CORS.

CORS controls which origins are allowed to access an API from a browser.

---

## 17. What is JWT?

A digitally signed token carrying identity and authorization claims.

---

## 18. Difference Between Authentication and Authorization?

Authentication verifies identity.

Authorization determines permissions.

---

## 19. What is OAuth?

OAuth enables delegated authorization without exposing user passwords.

---

## 20. What is OpenAPI?

A standard specification for documenting REST APIs.

---

# Senior Engineer Level

## 21. How Would You Design a REST API?

Expected discussion:

- Resources
- URI design
- HTTP methods
- Status codes
- Versioning
- Error handling
- Pagination
- Security

---

## 22. REST vs GraphQL?

Discuss:

- Flexibility
- Performance
- Over-fetching
- Under-fetching
- Complexity

---

## 23. REST vs gRPC?

Discuss:

- JSON vs Protobuf
- Browser support
- Performance
- Streaming
- Internal communication

---

## 24. GraphQL vs REST vs gRPC—When Would You Choose Each?

Business requirements.

Should drive.

Technology selection.

---

## 25. Explain API Gateway.

Expected topics:

- Routing
- Authentication
- Rate limiting
- Aggregation
- Monitoring

---

## 26. Explain Backend for Frontend (BFF).

Separate backend optimized for each client type.

---

## 27. Explain API Composition.

Combines responses from multiple services into one client response.

---

## 28. Explain API Versioning Strategies.

Expected discussion:

- URI
- Header
- Query Parameter
- Media Type

---

## 29. Explain Consumer-Driven Contracts.

Consumers define expectations.

Providers verify compatibility before deployment.

---

## 30. Explain OpenTelemetry.

Standard for collecting metrics, logs, and traces across distributed systems.

---

# Solution Architect Level

## 31. Design APIs for an E-Commerce Platform.

Expected discussion:

- API Gateway
- BFF
- REST
- GraphQL
- Authentication
- Versioning
- Pagination
- Rate Limiting

---

## 32. Design Public APIs for Millions of Users.

Topics:

- API Gateway
- CDN
- Caching
- Rate limiting
- Autoscaling
- Monitoring

---

## 33. How Would You Secure Public APIs?

Expected discussion:

- OAuth 2.0
- OIDC
- JWT
- TLS
- Rate limiting
- Input validation
- WAF
- Audit logging

---

## 34. Design APIs for Mobile Applications.

Discuss:

- BFF
- GraphQL
- Payload optimization
- Compression
- Caching

---

## 35. Design APIs for Internal Microservices.

Expected discussion:

- gRPC
- Protobuf
- Service Mesh
- mTLS
- Observability

---

## 36. REST vs Messaging?

Expected discussion:

- Synchronous communication
- Asynchronous workflows
- Event-driven architecture
- Reliability
- Coupling

---

## 37. How Would You Version APIs Without Breaking Clients?

Discuss:

- Backward compatibility
- Deprecation
- Migration
- Consumer communication

---

## 38. How Would You Handle Third-Party API Failures?

Expected discussion:

- Timeout
- Retry
- Circuit Breaker
- Fallback
- Monitoring

---

## 39. How Would You Monitor APIs?

Topics:

- Metrics
- Logs
- Traces
- SLIs
- SLOs
- Alerts

---

## 40. Explain API Governance.

Expected discussion:

- Standards
- Documentation
- Security
- Versioning
- Review process
- API catalog

---

# Principal Architect Level

## 41. Design an Enterprise API Platform.

Expected discussion:

- API Gateway
- Service Mesh
- Governance
- Security
- Observability
- Developer Portal
- API Catalog

---

## 42. Design APIs for Global Scale.

Topics:

- Multi-region deployment
- Global load balancing
- CDN
- Disaster recovery
- Data residency
- Compliance

---

## 43. REST, GraphQL, gRPC, WebSocket, and Messaging—How Would You Combine Them?

Architectural decision.

Based on:

- Consumers
- Latency
- Throughput
- Communication model
- Business requirements

---

## 44. How Would You Build an API Governance Platform?

Expected discussion:

- OpenAPI
- AsyncAPI
- API review board
- Contract testing
- Version lifecycle
- Security standards

---

## 45. How Would You Design APIs for AI Agents?

Discuss:

- Stable contracts
- Tool discoverability
- Idempotent operations
- Long-running workflows
- Async callbacks
- Rate limits
- Auditability

---

# Common Follow-up Questions

Interviewers often ask:

- Why REST instead of GraphQL?
- Why GraphQL instead of REST?
- Why gRPC for internal communication?
- Why use an API Gateway?
- Why use BFF?
- What is HATEOAS?
- How do you version APIs?
- How do you prevent duplicate payments?
- How do you secure APIs?
- How do you monitor APIs?
- What should be cached?
- What should never be cached?

Always relate your answer to:

- Business requirements
- Scalability
- Security
- Maintainability
- Performance
- Operational complexity

---

# 81. One-Page Revision Cheat Sheet

## Core Concepts

- APIs expose business capabilities through stable contracts.
- REST models business entities as resources.
- Resources should be nouns, not verbs.
- Use correct HTTP methods.
- Use proper HTTP status codes.
- APIs should be stateless.
- Pagination is mandatory for large datasets.
- Design idempotent operations for critical workflows.
- API Gateway centralizes cross-cutting concerns.
- BFF optimizes APIs for different clients.
- GraphQL solves over-fetching and under-fetching.
- gRPC provides high-performance service-to-service communication.
- WebSockets support bidirectional real-time communication.
- OpenAPI documents REST APIs.
- AsyncAPI documents event-driven APIs.
- Governance ensures long-term consistency.

---

# 82. Architect's Golden Principles

1. Design APIs around business resources, not database tables.
2. Treat APIs as long-lived business contracts.
3. Choose communication styles based on business needs—not technology trends.
4. Use REST for broad interoperability.
5. Use GraphQL when clients require flexible data retrieval.
6. Use gRPC for high-performance internal communication.
7. Secure every API with defense in depth.
8. Design for backward compatibility from day one.
9. Make APIs observable with metrics, logs, and traces.
10. Optimize for consistency across the API ecosystem, not just individual endpoints.

---

# 83. Chapter Completion Checklist

```markdown
- [x] API Foundations
- [x] Business Motivation
- [x] REST Principles
- [x] Richardson Maturity Model
- [x] Resources
- [x] URI Design
- [x] HTTP Methods
- [x] HTTP Status Codes
- [x] Request Structure
- [x] Response Structure
- [x] HTTP Headers
- [x] Content Negotiation
- [x] Error Handling
- [x] Pagination
- [x] Filtering
- [x] Sorting
- [x] Searching
- [x] Partial Responses
- [x] Idempotency
- [x] HATEOAS
- [x] GraphQL
- [x] GraphQL Federation
- [x] gRPC
- [x] Protocol Buffers
- [x] Unary RPC
- [x] Streaming RPC
- [x] WebSockets
- [x] Server-Sent Events (SSE)
- [x] Webhooks
- [x] Async APIs
- [x] Authentication
- [x] Authorization
- [x] OAuth 2.0
- [x] OpenID Connect (OIDC)
- [x] JWT
- [x] mTLS
- [x] API Keys
- [x] CORS
- [x] CSRF
- [x] Input Validation
- [x] Rate Limiting
- [x] API Gateway
- [x] Request Routing
- [x] Backend for Frontend (BFF)
- [x] API Composition
- [x] Aggregator Pattern
- [x] Facade Pattern
- [x] Anti-Corruption Layer
- [x] OpenAPI
- [x] Swagger
- [x] AsyncAPI
- [x] Contract-First Development
- [x] Consumer-Driven Contracts
- [x] API Versioning
- [x] Backward Compatibility
- [x] API Lifecycle Management
- [x] API Governance
- [x] Observability
- [x] Performance Optimization
- [x] Capacity Planning
- [x] Cost Optimization
- [x] Production Incidents
- [x] Architecture Review Checklist
- [x] ADR
- [x] Enterprise Case Studies
- [x] Interview Questions
- [x] Revision Cheat Sheet
```

---

# 84. Chapter Summary

APIs are the communication backbone of modern software systems.

They enable:

- Applications to integrate
- Microservices to collaborate
- Partners to connect
- Mobile apps to consume business capabilities
- AI agents to automate workflows

A well-designed API is far more than an HTTP endpoint—it is a stable, evolvable business contract.

Throughout this chapter, we explored:

- REST principles and resource-oriented design
- HTTP semantics and status codes
- Pagination, filtering, sorting, and searching
- GraphQL and gRPC
- Real-time communication patterns
- API security and identity
- API Gateway and enterprise integration patterns
- Governance, documentation, and versioning
- Production operations and observability

The central lesson is that **good API design balances developer experience, business needs, performance, security, and long-term maintainability**.

---

# Connection to Previous Chapters

| Chapter | Primary Question |
|----------|------------------|
| High Availability | Can users reach the system? |
| Reliability | Can users trust the system? |
| Scalability | Can the system grow? |
| Performance | Can it respond quickly? |
| Fault Tolerance | Can it survive failures? |
| Resilience | Can it recover and adapt? |
| Consistency | Is the data correct? |
| Distributed Transactions | How do distributed operations stay consistent? |
| Concurrency | How do many users work safely at the same time? |
| Data Management | How is business data stored and governed? |
| Caching | How is latency reduced? |
| Messaging & EDA | How do services communicate asynchronously? |
| **API Design & Communication** | **How do systems expose business capabilities through stable, secure, and scalable contracts?** |

APIs bridge the gap between users, applications, services, and business capabilities.

They are the external and internal contracts upon which distributed systems are built.

---

# Transition to Chapter 14

With API Design complete, the next logical topic is:

## **Chapter 14 – Microservices Architecture**

This chapter should cover:

### Foundations
- Monolith vs Microservices
- Evolution of software architecture
- Characteristics of microservices
- Domain-Driven Design (DDD)
- Bounded Contexts

### Core Patterns
- Database per Service
- Shared Database Anti-pattern
- Saga
- CQRS
- Event Sourcing
- Service Discovery
- Centralized Configuration
- API Gateway integration
- Service Mesh integration

### Operational Concerns
- Deployment strategies
- Containerization
- Kubernetes
- Autoscaling
- Resilience
- Observability
- Security
- Multi-tenancy

### Enterprise Topics
- Organizational design (Conway's Law)
- Team Topologies
- Strangler Fig Pattern
- Monolith decomposition
- Data migration strategies
- Governance
- Cost optimization
- Production case studies
- Interview preparation

---

# Suggested Advanced Topics

Future editions may include:

- API monetization
- Developer portals
- AsyncAPI deep dive
- GraphQL Federation at scale
- gRPC service mesh integration
- API analytics
- Zero Trust API architecture
- AI Agent APIs and MCP (Model Context Protocol)

---

> **Chapter 13 – API Design & Communication Complete**
