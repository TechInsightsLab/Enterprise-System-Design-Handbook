# 12. Failure Modes

High Availability is not achieved by assuming systems will never fail.

It is achieved by **assuming that failures are inevitable** and designing systems that continue delivering business value despite those failures.

One of the defining characteristics of experienced architects is that they spend considerable time asking:

> **"What can fail?"**

rather than

> **"What technology should we use?"**

Every production system eventually experiences failures.

Examples include:

* Hardware failures
* Software bugs
* Network outages
* Human mistakes
* Configuration errors
* Cloud provider incidents
* Resource exhaustion
* Unexpected traffic spikes
* Third-party service failures

Architects identify these risks early and design mitigation strategies before the system enters production.

---

## Failure Lifecycle

Every production failure typically follows a predictable lifecycle.

```text
Failure Occurs
      │
      ▼
Failure Detection
      │
      ▼
Impact Assessment
      │
      ▼
Traffic Isolation
      │
      ▼
Automatic Recovery
      │
      ▼
Service Restoration
      │
      ▼
Post-Incident Review
      │
      ▼
Architecture Improvements
```

A mature organization optimizes every stage of this lifecycle.

---

# 12.1 Network Failure

## Business Scenario

A payment service communicates with a fraud detection service over the network.

A network switch unexpectedly fails.

Although both services remain operational, they can no longer communicate.

Customers experience payment failures despite all application servers being healthy.

---

## Why It Happens

Common causes include:

* Router failure
* Switch failure
* Fiber cable damage
* Firewall misconfiguration
* DNS routing problems
* Cloud networking issues
* BGP routing incidents
* Packet loss
* High network latency

In distributed systems, **communication failures are far more common than application failures**.

---

## Symptoms

* Request timeouts
* Connection refused errors
* High latency
* Increased retries
* Service-to-service communication failures
* Growing request queues
* Partial functionality

---

## Detection

Typical monitoring includes:

* Network latency
* Packet loss percentage
* TCP connection failures
* HTTP timeout metrics
* Service mesh telemetry
* Distributed tracing
* Synthetic health checks

---

## Prevention

Architectural approaches include:

* Multiple network paths
* Redundant switches
* Multi-AZ deployment
* Service mesh with retries
* Intelligent timeout configuration
* Connection pooling
* Circuit breakers

---

## Recovery

Recovery strategies include:

* Route traffic through healthy paths.
* Fail over to another Availability Zone.
* Restart affected network devices.
* Automatically reroute traffic using load balancers.
* Isolate unhealthy services.

---

## Business Impact

Without redundancy:

* Checkout fails.
* Payments fail.
* Customer trust declines.
* Revenue decreases.

---

# 12.2 Region Failure

## Business Scenario

A cloud provider experiences a complete regional outage.

Every application instance is deployed in that single region.

The business becomes completely unavailable.

---

## Why It Happens

Possible causes include:

* Large-scale power failures
* Natural disasters
* Cloud infrastructure failures
* Regional networking outages
* Human operational mistakes
* Major software deployment failures

Although rare, regional failures have occurred across all major cloud providers.

---

## Symptoms

* Complete application outage
* Database unavailable
* Storage inaccessible
* DNS failures
* Authentication unavailable

---

## Detection

* Global monitoring
* Regional health checks
* External synthetic monitoring
* Multi-region heartbeat systems

---

## Prevention

Architectural options:

* Multi-region deployment
* Active-Active architecture
* Active-Passive architecture
* Cross-region database replication
* Global DNS failover

---

## Recovery

Typical recovery:

1. Detect regional outage.
2. Redirect traffic.
3. Promote secondary databases.
4. Resume traffic.
5. Verify data consistency.

---

## Business Impact

Organizations operating globally often measure outages in **millions of dollars per hour**.

---

# 12.3 Availability Zone (AZ) Failure

Availability Zones are designed to isolate localized infrastructure failures.

An entire AZ may become unavailable because of:

* Power failure
* Cooling failure
* Network isolation
* Internal cloud maintenance issues

---

## Prevention

Deploy application instances across multiple AZs.

Example:

```text
                Internet
                     │
                     ▼
            Load Balancer
           /             \
          ▼               ▼
      AZ-A             AZ-B
   App Servers      App Servers
        │               │
        └──────┬────────┘
               ▼
     Multi-AZ Database Cluster
```

If one AZ becomes unavailable, traffic automatically shifts to healthy instances.

---

# 12.4 DNS Failure

## Business Scenario

Users cannot reach the application because DNS resolution fails.

The application itself is healthy.

---

## Common Causes

* DNS provider outage
* Incorrect DNS records
* Expired domains
* Cache poisoning
* Misconfigured TTL

---

## Symptoms

* "Website not found"
* Name resolution timeout
* Intermittent connectivity

---

## Prevention

* Multiple DNS providers
* Low TTL values
* Global Anycast DNS
* Continuous DNS monitoring

---

## Business Impact

Customers perceive the entire platform as unavailable.

---

# 12.5 Database Failure

The database is frequently the most critical component in enterprise systems.

If every service depends on one database, it becomes a **Single Point of Failure (SPOF).**

---

## Common Causes

* Hardware failure
* Disk corruption
* Storage exhaustion
* Database software crash
* Network partition
* Replication failure
* Human error

---

## Symptoms

* Slow queries
* Connection failures
* Transaction failures
* Deadlocks
* Replication lag

---

## Prevention

* Database replication
* Automatic failover
* Read replicas
* Backup strategy
* Connection pooling
* Health monitoring

---

## Recovery

* Promote replica
* Restore from backup (if necessary)
* Redirect application connections
* Validate data integrity

---

## Business Impact

For transactional systems, database failures frequently result in complete service interruption.

---

# 12.6 Cache Failure

Caches improve performance but should never become mandatory for correctness.

---

## Business Scenario

Redis cluster becomes unavailable.

Applications incorrectly assume cached data always exists.

Requests fail instead of falling back to the database.

---

## Prevention

Architectural principle:

> Cache should accelerate the system, not define system correctness.

---

## Recovery

Applications should:

* Detect cache failure
* Read from database
* Gradually warm cache
* Avoid overwhelming the database

---

## Business Impact

If improperly designed:

Cache failure becomes application failure.

---

# 12.7 Deployment Failure

Many outages originate from deployments rather than infrastructure.

---

## Causes

* Incorrect configuration
* Software bugs
* Schema incompatibility
* Dependency mismatch
* Feature flag mistakes

---

## Prevention

* Blue-Green deployment
* Canary releases
* Rolling deployment
* Automated rollback
* Progressive delivery

---

## Recovery

Immediately rollback.

Do not debug in production while customers wait.

---

# 12.8 Human Error

Studies consistently show that human mistakes contribute significantly to production incidents.

Examples include:

* Deleting production databases
* Incorrect firewall rules
* Wrong DNS configuration
* Deploying to wrong environment
* Expired certificates
* Incorrect IAM permissions

---

## Prevention

* Infrastructure as Code
* Change reviews
* Peer approvals
* Automation
* Least privilege access

---

# 12.9 Disk Failure

Storage devices eventually fail.

Although cloud providers abstract hardware management, disks still experience failures.

---

## Symptoms

* Read/write errors
* High latency
* Database corruption
* Filesystem failures

---

## Prevention

* RAID
* Replication
* Snapshots
* Multi-volume storage

---

# 12.10 Memory Exhaustion

Applications gradually consume available memory until they can no longer process requests.

Common reasons include:

* Memory leaks
* Large caches
* Unbounded collections
* Excessive buffering

---

## Symptoms

* Increased garbage collection
* High response time
* OutOfMemoryError
* Process restart

---

## Prevention

* Memory monitoring
* Heap analysis
* Resource limits
* Load testing

---

# 12.11 CPU Saturation

During unexpected traffic spikes:

* CPU reaches 100%
* Thread pools become exhausted
* Requests queue
* Response times increase dramatically

---

## Prevention

* Horizontal scaling
* Autoscaling
* Efficient algorithms
* Request throttling
* Load shedding

---

# 12.12 Slow Dependency

A dependency does not need to fail to cause an outage.

Sometimes it merely becomes slow.

Example:

```
Order Service
      │
      ▼
Payment Service
      │
      ▼
Fraud Service
      │
      ▼
Third-Party API
```

If the third-party API slows from **100 ms** to **20 seconds**, every upstream service eventually becomes blocked.

---

## Prevention

* Timeouts
* Circuit breakers
* Bulkheads
* Async processing
* Graceful degradation

---

# 12.13 Retry Storm

Retries improve resilience—but excessive retries can amplify failures.

## Example

```
1 Failed Request

↓

5 Automatic Retries

↓

25 Downstream Requests

↓

125 Requests

↓

625 Requests

↓

Service Collapse
```

Instead of recovering, the system overwhelms the already struggling dependency.

---

## Prevention

* Exponential backoff
* Jitter
* Retry budgets
* Circuit breakers
* Rate limiting

---

# 12.14 Thundering Herd

A large number of clients simultaneously request the same resource after a cache expiration or service recovery.

Example:

```
1 Million Cache Entries Expire

↓

Database Receives

1 Million Queries

Immediately
```

The database becomes overloaded.

---

## Prevention

* Cache expiration jitter
* Request coalescing
* Cache warming
* Distributed locks
* Stale-while-revalidate

---

# 12.15 Split Brain

Split brain occurs when multiple nodes incorrectly believe they are the primary node.

Consequences include:

* Duplicate writes
* Data corruption
* Inconsistent state
* Lost transactions

---

## Prevention

* Quorum-based consensus
* Distributed coordination
* Leader election protocols
* Fencing tokens

---

# 12.16 Race Conditions

Multiple concurrent operations modify shared state without proper synchronization.

Examples:

* Double payment
* Duplicate order creation
* Overselling inventory

---

## Prevention

* Optimistic locking
* Pessimistic locking
* Atomic operations
* Idempotency
* Version control

---

# 12.17 Clock Skew

Distributed systems assume clocks are synchronized.

Unfortunately, they rarely are.

Clock differences affect:

* Authentication
* Token expiration
* Event ordering
* Distributed transactions
* Audit logs

---

## Prevention

* NTP synchronization
* Logical clocks
* Hybrid logical clocks
* Timestamp tolerance

---

# 12.18 Duplicate Requests

Clients frequently resend requests after timeouts.

Without idempotency:

* Orders may be duplicated.
* Payments may be charged twice.
* Inventory becomes inconsistent.

---

## Prevention

* Idempotency keys
* Request deduplication
* Transaction identifiers

---

# 12.19 Hot Partition

One database partition receives significantly more traffic than others.

Example:

```
Customer A

↓

80% of Traffic

↓

Partition 1

Other Partitions

↓

Almost Idle
```

---

## Prevention

* Better partition keys
* Consistent hashing
* Dynamic rebalancing
* Load-aware partitioning

---

# 12.20 Partial Failure

One of the defining characteristics of distributed systems is that **some components fail while others continue operating**.

Example:

* Inventory Service is healthy.
* Recommendation Service is healthy.
* Payment Service is unavailable.

The system is neither fully operational nor completely down.

Architects must decide:

* What functionality should continue?
* What can degrade gracefully?
* What should fail fast?

---

# 12.21 Cascading Failure

Cascading failures are among the most dangerous production incidents.

```
Database Slow

↓

Application Threads Block

↓

Connection Pool Exhausted

↓

API Timeout

↓

Retries Increase

↓

CPU Saturation

↓

Load Balancer Removes Instances

↓

Remaining Instances Overloaded

↓

Entire Platform Down
```

A minor problem escalates into a platform-wide outage.

---

## Prevention

* Timeouts
* Circuit breakers
* Bulkheads
* Load shedding
* Backpressure
* Graceful degradation
* Retry limits
* Autoscaling

---

## Key Architectural Lesson

An experienced architect does not ask:

> **"How do I prevent failures?"**

Instead, they ask:

* Which failures are inevitable?
* Which failures are acceptable?
* Which failures must be invisible to customers?
* How quickly can we detect them?
* How quickly can we recover?
* How can we prevent a localized failure from becoming a business-wide outage?

These questions form the foundation of High Availability architecture and guide every subsequent design decision.

---

**End of Part 3.**

The next part will cover **Section 13: Architecture Decisions**, explaining *why* architects choose patterns such as load balancers, API gateways, redundancy, CQRS, Saga, circuit breakers, bulkheads, retries, timeouts, rate limiting, multi-AZ, multi-region, sharding, service discovery, health checks, and other architectural mechanisms before discussing implementation technologies.
