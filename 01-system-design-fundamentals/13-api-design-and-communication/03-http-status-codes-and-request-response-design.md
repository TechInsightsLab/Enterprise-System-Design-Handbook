
# 6. HTTP Status Codes

> **HTTP Status Codes are standardized response codes that indicate the outcome of an API request.**

A well-designed API does not communicate success or failure only through JSON.

It also uses proper HTTP status codes.

This allows:

- Browsers
- Mobile Apps
- API Gateways
- SDKs
- Monitoring tools

to immediately understand the result.

---

# Why Status Codes Matter

Imagine an API returns:

```json
{
    "status":"SUCCESS"
}
```

But the HTTP status is:

```
500
```

Which one should the client trust?

The HTTP status code.

Always.

---

# Status Code Categories

```text
1xx → Informational

2xx → Success

3xx → Redirection

4xx → Client Errors

5xx → Server Errors
```

Most enterprise APIs primarily use:

- 2xx
- 4xx
- 5xx

---

# 1xx – Informational

Temporary responses.

Rarely used in REST APIs.

Example.

```
100 Continue
```

---

# 2xx – Success

Request completed successfully.

---

## 200 OK

Most common response.

Used for:

```
GET

PUT

PATCH
```

Example.

```http
GET /products/100

200 OK
```

---

## 201 Created

Resource successfully created.

Typically returned after:

```
POST
```

Example.

```http
POST /orders

201 Created
```

Include:

```
Location

/orders/500
```

header whenever possible.

---

## 202 Accepted

Request accepted.

Processing happens later.

Usually used for:

- Background jobs
- Asynchronous processing
- Messaging systems

Example.

```text
Upload Video

↓

202 Accepted

↓

Encoding Starts
```

---

## 204 No Content

Operation succeeded.

No response body.

Typical for:

```
DELETE
```

---

# Success Code Summary

| Code | Meaning |
|--------|---------|
| 200 | Success |
| 201 | Resource Created |
| 202 | Accepted for Processing |
| 204 | Success, No Content |

---

# 3xx – Redirection

Mostly handled by browsers.

Rare in REST APIs.

Examples.

```
301

302

304
```

---

# 4xx – Client Errors

Client sent.

Invalid request.

---

## 400 Bad Request

Malformed request.

Examples.

- Invalid JSON
- Missing fields
- Invalid parameter

---

## 401 Unauthorized

Authentication required.

User identity unknown.

Example.

Missing JWT.

---

## 403 Forbidden

User authenticated.

Not authorized.

Example.

Customer tries.

```
DELETE /admin
```

---

## 404 Not Found

Requested resource.

Does not exist.

Example.

```
GET /orders/999999
```

---

## 405 Method Not Allowed

Wrong HTTP method.

Example.

```
PUT

On

Read-only endpoint.
```

---

## 406 Not Acceptable

Requested response format.

Not supported.

---

## 409 Conflict

Business conflict.

Examples.

- Duplicate email
- Version conflict
- Optimistic locking failure

---

## 410 Gone

Resource permanently removed.

Useful for deprecated APIs.

---

## 412 Precondition Failed

Usually used with conditional requests.

Examples.

ETag mismatch.

Optimistic concurrency.

---

## 415 Unsupported Media Type

Wrong content type.

Example.

```
text/plain
```

Instead of.

```
application/json
```

---

## 422 Unprocessable Entity

Request syntax correct.

Business validation failed.

Examples.

- Negative quantity
- Invalid account state
- Business rule violation

Very common.

In modern APIs.

---

## 429 Too Many Requests

Rate limit exceeded.

Client should retry later.

---

# Client Error Summary

| Code | Meaning |
|--------|----------|
| 400 | Invalid Request |
| 401 | Authentication Required |
| 403 | Permission Denied |
| 404 | Resource Not Found |
| 405 | Invalid HTTP Method |
| 409 | Business Conflict |
| 410 | Resource Removed |
| 412 | Precondition Failed |
| 415 | Unsupported Media Type |
| 422 | Validation Failed |
| 429 | Rate Limit Exceeded |

---

# 5xx – Server Errors

Server failed.

Client request.

May be valid.

---

## 500 Internal Server Error

Unexpected exception.

Most generic error.

Should not expose internal details.

---

## 501 Not Implemented

Requested functionality.

Not implemented.

Rare.

---

## 502 Bad Gateway

Gateway received.

Invalid response.

From downstream service.

Often seen with API Gateways.

---

## 503 Service Unavailable

Service temporarily unavailable.

Examples.

- Maintenance
- Overloaded service
- Circuit breaker open

---

## 504 Gateway Timeout

Gateway waited.

Backend.

Did not respond.

---

# Server Error Summary

| Code | Meaning |
|--------|----------|
| 500 | Internal Error |
| 501 | Feature Not Implemented |
| 502 | Bad Gateway |
| 503 | Service Unavailable |
| 504 | Gateway Timeout |

---

# Choosing Correct Status Codes

| Scenario | Status |
|-----------|---------|
| Read Success | 200 |
| Create Resource | 201 |
| Async Job | 202 |
| Delete Success | 204 |
| Invalid JSON | 400 |
| Login Required | 401 |
| Permission Denied | 403 |
| Resource Missing | 404 |
| Duplicate Data | 409 |
| Validation Failure | 422 |
| Too Many Requests | 429 |
| Unexpected Error | 500 |

---

# 7. HTTP Request Structure

Every HTTP request consists of:

```text
Method

↓

URI

↓

Headers

↓

Body (Optional)
```

---

# Example

```http
POST /orders HTTP/1.1

Host: api.company.com

Authorization: Bearer ey...

Content-Type: application/json

Accept: application/json

{
  "customerId":101,
  "items":[]
}
```

---

# Request Components

## HTTP Method

Defines operation.

```
GET

POST

PUT

PATCH

DELETE
```

---

## URI

Identifies resource.

```
/orders/100
```

---

## Headers

Provide metadata.

Examples.

```
Authorization

Accept

Content-Type

If-Match

User-Agent

Correlation-ID
```

---

## Request Body

Contains data.

Mostly used by:

```
POST

PUT

PATCH
```

---

# Request Validation

Validate:

- Headers
- Authentication
- Required fields
- Business rules
- Data types
- Payload size

Never trust client input.

---

# 8. HTTP Response Structure

Every response contains:

```text
Status

↓

Headers

↓

Body
```

---

# Example

```http
HTTP/1.1 200 OK

Content-Type: application/json

Cache-Control: max-age=300

{
 "id":100,
 "status":"PLACED"
}
```

---

# Response Design Principles

Good responses are:

- Consistent
- Predictable
- Minimal
- Well documented

---

# Success Response

```json
{
  "id":100,
  "status":"SUCCESS"
}
```

---

# Error Response

Avoid.

```json
{
 "error":"Something went wrong"
}
```

---

Better.

```json
{
  "timestamp":"2026-07-09T12:10:00Z",
  "status":422,
  "error":"Validation Failed",
  "message":"Quantity must be greater than zero",
  "path":"/orders"
}
```

---

# Standard Error Fields

Recommended.

- Timestamp
- Status
- Error
- Message
- Path
- Correlation ID
- Error Code (business-specific)

---

# Example

```json
{
  "timestamp":"2026-07-09T12:10:00Z",
  "status":409,
  "error":"Conflict",
  "code":"ORDER_ALREADY_EXISTS",
  "message":"Order 100 already exists.",
  "path":"/orders/100",
  "correlationId":"a8c3b5d1"
}
```

---

# 9. HTTP Headers

Headers carry metadata.

Not business data.

---

# Common Request Headers

| Header | Purpose |
|----------|----------|
| Authorization | Authentication |
| Accept | Expected response format |
| Content-Type | Request body format |
| User-Agent | Client information |
| If-Match | Optimistic locking |
| Correlation-ID | Request tracing |

---

# Common Response Headers

| Header | Purpose |
|----------|----------|
| Content-Type | Response format |
| Cache-Control | Caching policy |
| Location | Created resource |
| ETag | Version identifier |
| Retry-After | Retry guidance |
| X-RateLimit-Remaining | Remaining quota (custom/common convention) |

---

# Authorization Header

Example.

```http
Authorization:

Bearer JWT_TOKEN
```

---

# Content-Type

Defines request format.

```
application/json

application/xml

multipart/form-data

application/octet-stream
```

---

# Accept Header

Client requests.

Preferred format.

```
Accept:

application/json
```

---

# Correlation ID

Very important.

For distributed systems.

Example.

```
X-Correlation-ID:

ABC123XYZ
```

Same ID.

Appears.

Across.

Logs.

Tracing.

Messaging.

Microservices.

---

# 10. Content Negotiation

> **Content negotiation allows clients and servers to agree on the representation of a resource.**

Example.

Client requests.

```
Accept:

application/json
```

Server returns.

JSON.

---

Another client.

Requests.

```
application/xml
```

Server returns.

XML.

---

# Common Formats

- JSON
- XML
- CSV
- PDF
- Binary

JSON is the dominant format for modern APIs.

---

# 11. Error Handling

Errors should be:

- Predictable
- Consistent
- Actionable

Clients should understand:

- What failed
- Why it failed
- What to do next

---

# Good Error Example

```json
{
  "status":422,
  "code":"INVALID_QUANTITY",
  "message":"Quantity must be greater than zero.",
  "correlationId":"ABC123XYZ"
}
```

---

# Bad Error Example

```json
{
 "error":"Exception"
}
```

Provides little value.

---

# Do Not Expose

Never expose:

- Stack traces
- SQL queries
- Internal server paths
- Passwords
- Secrets
- Framework internals

Return useful information.

Not sensitive information.

---

# Common Mistakes

### Returning 200 for Errors

```http
200 OK

{
 "status":"FAILED"
}
```

Misleading.

Use proper HTTP status codes.

---

### Returning 500 for Validation Errors

Validation failures are client errors.

Prefer:

```
400

or

422
```

---

### Leaking Internal Exceptions

Never expose.

```text
java.lang.NullPointerException
```

To clients.

Log internally.

Return sanitized responses.

---

### Inconsistent Error Formats

Every API should follow one error response standard.

Consistency simplifies client development.

---

# Architect's Perspective

HTTP status codes are part of the **API contract**, not an implementation detail.

A well-designed API communicates through:

- Correct status codes
- Clear response bodies
- Consistent error formats
- Meaningful headers

When APIs behave predictably, client applications become simpler, monitoring improves, and operational troubleshooting becomes significantly easier.

---

**End of Part 2B**

The next part will cover:

- **Pagination**
- **Filtering**
- **Sorting**
- **Searching**
- **Partial Responses**
- **Idempotency in APIs**
- **HATEOAS**
- **REST API Best Practices**
