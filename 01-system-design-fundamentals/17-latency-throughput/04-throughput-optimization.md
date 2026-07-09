
# 25. Horizontal Scaling

> **Horizontal Scaling increases system capacity by adding more machines or service instances instead of upgrading existing hardware.**

Instead of making.

One server.

Bigger.

We add.

More servers.

To share the workload.

---

# Vertical vs Horizontal Scaling

Vertical Scaling.

```text
Server

↓

8 CPU

↓

16 CPU

↓

32 CPU
```

One machine.

Becomes more powerful.

---

Horizontal Scaling.

```text
Server 1

+

Server 2

+

Server 3

+

Server 4
```

Multiple machines.

Share traffic.

---

# Why Horizontal Scaling?

Imagine.

An e-commerce website.

During a festival sale.

Traffic increases.

```text
10,000 Users

↓

100,000 Users

↓

1,000,000 Users
```

One server.

Cannot handle.

All requests.

Additional servers.

Increase throughput.

---

# Architecture

```text
Users

↓

Load Balancer

↓

App 1

↓

App 2

↓

App 3

↓

Database
```

Traffic spreads.

Across multiple instances.

---

# Benefits

Horizontal scaling provides.

- Higher throughput
- Better availability
- Improved fault tolerance
- Easier maintenance

---

# Challenges

Horizontal scaling introduces.

- Data consistency
- Session management
- Distributed coordination
- Load balancing
- Monitoring complexity

---

# Stateless Services

Horizontal scaling works best.

When services are.

Stateless.

Example.

```text
Any Request

↓

Any Instance
```

Requests.

Can reach.

Any server.

---

# Architect's Perspective

Modern cloud-native systems.

Prefer horizontal scaling.

Because it supports elasticity.

And fault tolerance.

---

# 26. Load Balancing

> **Load Balancing distributes incoming requests across multiple servers to maximize throughput, reduce latency, and improve availability.**

Without load balancing.

One server.

May become overloaded.

While others remain idle.

---

# Without Load Balancer

```text
Users

↓

Server

↓

100%

CPU
```

Other servers.

Receive no traffic.

---

# With Load Balancer

```text
Users

↓

Load Balancer

↓

Server A

↓

Server B

↓

Server C
```

Traffic is distributed.

Evenly.

---

# Benefits

Load balancing improves.

- Throughput
- Availability
- Resource utilization
- Fault tolerance

---

# Common Algorithms

### Round Robin

```text
1 → A

2 → B

3 → C

4 → A
```

Simple.

Fair.

---

### Least Connections

Traffic goes.

To the server.

Handling the fewest.

Active connections.

Useful.

For long-running requests.

---

### Weighted Round Robin

Powerful servers.

Receive.

More requests.

Example.

```text
Server A

Weight 3

----------------

Server B

Weight 1
```

---

### IP Hash

Requests.

From the same client.

Usually reach.

The same server.

Useful.

For session affinity.

---

# Health Checks

Load balancers.

Continuously verify.

Server health.

```text
Healthy

↓

Receive Traffic

----------------

Unhealthy

↓

Removed
```

Traffic avoids.

Failed servers.

Automatically.

---

# Architect's Perspective

A load balancer.

Improves throughput.

Only if downstream systems.

Can also scale.

---

# 27. Partitioning

> **Partitioning divides a large dataset into smaller logical partitions so that work can be processed more efficiently.**

Partitioning improves.

Performance.

Manageability.

And throughput.

---

# Example

Orders.

```text
Orders

↓

2024

↓

2025

↓

2026
```

Queries.

Search.

Smaller datasets.

---

# Partitioning Types

Examples.

- Range
- Hash
- List
- Composite

Choice depends.

On access patterns.

---

# Example

Customer IDs.

```text
1-100000

↓

Partition A

-------------------

100001-200000

↓

Partition B
```

---

# Benefits

Partitioning reduces.

- Query latency
- Index size
- Lock contention

And improves.

Maintenance.

---

# Architect's Perspective

Partitioning.

Usually occurs.

Within one database.

Sharding.

Extends the idea.

Across multiple databases.

---

# 28. Sharding

> **Sharding distributes data across multiple independent databases to increase storage capacity and throughput.**

Instead of.

One database.

Store data.

Across many.

---

# Example

```text
Shard 1

Customer 1-1M

-------------------

Shard 2

Customer 1M-2M

-------------------

Shard 3

Customer 2M-3M
```

Each shard.

Handles only.

Part of the workload.

---

# Throughput

Without sharding.

```text
One Database

↓

50,000 TPS
```

---

With four shards.

```text
Shard A

↓

Shard B

↓

Shard C

↓

Shard D
```

Potential throughput.

Increases significantly.

If the workload distributes evenly.

---

# Challenges

Sharding introduces.

- Cross-shard queries
- Rebalancing
- Data migration
- Routing
- Distributed transactions

---

# Architect's Perspective

Sharding increases throughput.

But also.

Adds operational complexity.

Use only.

When necessary.

---

# 29. Worker Pools

> **A Worker Pool is a group of workers that process tasks concurrently from a shared queue.**

Instead of.

One worker.

Processing.

Every task.

Multiple workers.

Operate in parallel.

---

# Architecture

```text
Producer

↓

Queue

↓

Worker 1

↓

Worker 2

↓

Worker 3

↓

Worker 4
```

Throughput increases.

As more workers.

Process tasks.

---

# Examples

Worker pools.

Process.

- Emails
- Images
- Videos
- Reports
- Payments
- Notifications

---

# Pool Size

Too few workers.

Low throughput.

Too many workers.

Contention.

Context switching.

Higher resource usage.

Optimal sizing.

Depends on workload.

---

# Architect's Perspective

Worker pools are one of the simplest.

And most effective.

Ways.

To improve throughput.

---

# 30. Parallel Processing

> **Parallel Processing executes multiple independent operations simultaneously using multiple CPU cores, threads, or machines.**

Sequential execution.

```text
Task A

↓

Task B

↓

Task C
```

---

Parallel execution.

```text
Task A

↓

Task B

↓

Task C

(All Together)
```

---

# Enterprise Examples

Parallel processing.

Used in.

- Batch jobs
- Analytics
- AI inference
- Report generation
- Image processing

---

# Benefits

Parallelism increases.

- CPU utilization
- Throughput
- Overall processing capacity

---

# Trade-Off

Parallel systems.

Require.

Synchronization.

Coordination.

Thread safety.

---

# Architect's Perspective

Not every workload.

Can be parallelized.

Identify independent tasks.

Before introducing concurrency.

---

# 31. Queue-Based Architectures

Queues decouple.

Producers.

From consumers.

Allowing systems.

To absorb bursts.

Without immediate processing.

---

# Architecture

```text
Producer

↓

Queue

↓

Consumers
```

The producer.

Does not wait.

For processing.

---

# Benefits

Queues improve.

- Throughput
- Reliability
- Scalability
- Resilience

---

# Burst Handling

Imagine.

10,000 requests.

Arrive.

In one second.

Workers process.

1,000 per second.

Queue stores.

Remaining work.

Without dropping requests.

---

# Common Technologies

Examples.

- Kafka
- RabbitMQ
- ActiveMQ
- Amazon SQS
- Azure Service Bus

---

# Architect's Perspective

Queues convert.

Traffic spikes.

Into manageable workloads.

---

# 32. Backpressure

> **Backpressure is a mechanism that prevents producers from overwhelming consumers when the rate of incoming work exceeds processing capacity.**

Without backpressure.

Queues grow.

Memory increases.

Eventually.

Systems fail.

---

# Example

Producer.

```text
10,000 msg/sec
```

Consumer.

```text
2,000 msg/sec
```

Queue.

Continuously grows.

---

# Backpressure Strategies

Examples.

- Slow producers
- Reject requests
- Rate limiting
- Buffering
- Load shedding

---

# Benefits

Backpressure protects.

System stability.

Even during overload.

---

# Architect's Perspective

High throughput.

Without backpressure.

Eventually becomes.

System failure.

---

# 33. Flow Control

> **Flow Control regulates the amount of data transmitted between communicating systems to prevent receivers from becoming overloaded.**

Although similar.

Flow control.

Usually operates.

At the protocol.

Or transport level.

---

# Example

Sender.

```text
100 MB/sec
```

Receiver.

```text
20 MB/sec
```

Flow control.

Adjusts transmission.

Preventing buffer overflow.

---

# Examples

Flow control exists.

In.

- TCP
- HTTP/2
- gRPC
- Kafka
- Reactive Streams

---

# Benefits

Flow control provides.

- Stability
- Predictable throughput
- Reduced packet loss
- Better resource utilization

---

# Architect's Perspective

Flow control.

Protects communication channels.

Backpressure.

Protects applications.

Together.

They improve resilience.

---

# 34. Autoscaling

> **Autoscaling automatically adjusts computing resources based on workload demand.**

Instead of.

Provisioning.

For peak traffic.

Resources grow.

And shrink.

Automatically.

---

# Example

Morning.

```text
3 Instances
```

Afternoon.

```text
15 Instances
```

Night.

```text
4 Instances
```

Capacity follows demand.

---

# Common Scaling Metrics

Examples.

- CPU
- Memory
- Requests/sec
- Queue length
- Kafka lag
- Custom business metrics

---

# Reactive Autoscaling

```text
High CPU

↓

Scale Out
```

Responds.

After load increases.

---

# Predictive Autoscaling

```text
Historical Patterns

↓

Forecast

↓

Scale Before Traffic
```

Useful.

For predictable events.

Such as.

Festival sales.

---

# Benefits

Autoscaling provides.

- Better throughput
- Lower cost
- Improved elasticity
- Better availability

---

# Trade-Off

Scaling takes time.

Cold starts.

May temporarily.

Increase latency.

---

# Throughput Optimization Summary

| Technique | Primary Benefit |
|-----------|-----------------|
| Horizontal Scaling | More processing capacity |
| Load Balancing | Even workload distribution |
| Partitioning | Faster data access |
| Sharding | Database scalability |
| Worker Pools | Concurrent task execution |
| Parallel Processing | Higher CPU utilization |
| Queue-Based Architecture | Smooth burst handling |
| Backpressure | Prevent overload |
| Flow Control | Stable communication |
| Autoscaling | Elastic capacity |

---

# Common Throughput Anti-Patterns

### Scaling the Application Only

Application servers scale.

Database remains.

A bottleneck.

Scale the entire system.

---

### Unlimited Queue Growth

Queues are not infinite.

Monitor queue depth.

Implement backpressure.

---

### Too Many Threads

More threads.

Do not always.

Increase throughput.

Excessive context switching.

Can reduce performance.

---

### Ignoring Hot Partitions

Uneven partitioning.

Creates hotspots.

Even when capacity exists elsewhere.

---

### Autoscaling Without Observability

Scaling decisions.

Require accurate metrics.

Otherwise.

Resources may scale.

Too late.

Or unnecessarily.

---

# Architect's Perspective

Improving throughput is fundamentally about **processing more useful work with the available resources**.

This is achieved not by a single optimization, but by combining multiple architectural techniques:

- Distribute traffic with load balancers.
- Spread data using partitioning and sharding.
- Execute work concurrently with worker pools.
- Decouple producers and consumers through queues.
- Protect the system using backpressure and flow control.
- Adjust capacity dynamically with autoscaling.

A mature architecture delivers **high throughput without sacrificing reliability, stability, or predictable latency**, even under rapidly changing workloads.

---

**End of Part 3**

The next part will cover:

- **Average vs Median**
- **Percentiles (P50, P95, P99, P99.9)**
- **Tail Latency**
- **Little's Law**
- **Queueing Theory Basics**
- **Capacity Planning**
- **Benchmarking**
- **Load Testing**
- **Stress Testing**
- **Spike Testing**
- **Soak Testing**
