

# Enterprise System Design Handbook – Glossary (Part 1)

| Term                                        | Definition                                                                                              |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **Availability**                            | The percentage of time a system is operational and accessible to users.                                 |
| **High Availability (HA)**                  | Designing systems to remain available even when components fail.                                        |
| **Downtime**                                | The period during which a system is unavailable.                                                        |
| **SLA (Service Level Agreement)**           | A contractual commitment defining expected service availability and performance.                        |
| **SLO (Service Level Objective)**           | A target value for a service metric such as latency or availability.                                    |
| **SLI (Service Level Indicator)**           | A measurable metric used to evaluate service quality (e.g., P99 latency).                               |
| **Reliability**                             | The ability of a system to consistently perform its intended function correctly over time.              |
| **Failure**                                 | The inability of a component or system to perform its expected function.                                |
| **Fault**                                   | The underlying defect or issue that can lead to a failure.                                              |
| **Error**                                   | An incorrect internal state caused by a fault.                                                          |
| **MTBF (Mean Time Between Failures)**       | Average operating time between failures of a repairable system.                                         |
| **MTTF (Mean Time To Failure)**             | Average lifetime of a non-repairable component before failure.                                          |
| **MTTR (Mean Time To Recovery/Repair)**     | Average time required to restore a failed system to normal operation.                                   |
| **Scalability**                             | The ability of a system to handle increasing workloads efficiently.                                     |
| **Vertical Scaling**                        | Increasing system capacity by upgrading CPU, memory, or storage of an existing machine.                 |
| **Horizontal Scaling**                      | Increasing capacity by adding more servers or service instances.                                        |
| **Elasticity**                              | Automatically scaling resources up or down based on workload demand.                                    |
| **Performance**                             | How efficiently a system processes requests while meeting user expectations.                            |
| **Latency**                                 | The time required to complete a single request or operation.                                            |
| **Response Time**                           | Total time experienced by the client from sending a request until receiving the complete response.      |
| **Throughput**                              | The amount of work a system completes in a given period (e.g., requests per second).                    |
| **Bandwidth**                               | The theoretical maximum data transfer capacity of a communication channel.                              |
| **Fault Tolerance**                         | The ability of a system to continue operating despite component failures.                               |
| **Resilience**                              | The ability of a system to recover quickly from failures while maintaining acceptable service.          |
| **Recovery**                                | Restoring a system to normal operation after a failure.                                                 |
| **Graceful Degradation**                    | Continuing to provide reduced functionality instead of failing completely.                              |
| **Consistency**                             | The guarantee that users observe correct and expected data after updates.                               |
| **Strong Consistency**                      | Every read immediately returns the most recent successful write.                                        |
| **Eventual Consistency**                    | Data replicas become consistent over time rather than immediately.                                      |
| **Read-After-Write Consistency**            | A client immediately sees its own successful write.                                                     |
| **Replication**                             | Maintaining multiple copies of data for availability, durability, or performance.                       |
| **Distributed Transaction**                 | A transaction spanning multiple independent services or databases.                                      |
| **Two-Phase Commit (2PC)**                  | A protocol ensuring all participants either commit or roll back a distributed transaction together.     |
| **Saga Pattern**                            | A sequence of local transactions coordinated using events or orchestration with compensating actions.   |
| **Compensating Transaction**                | An operation that semantically reverses a previously completed action.                                  |
| **Concurrency**                             | Multiple operations making progress during overlapping time periods.                                    |
| **Parallelism**                             | Executing multiple operations simultaneously using multiple processing units.                           |
| **Race Condition**                          | Incorrect behavior caused by multiple operations accessing shared data concurrently.                    |
| **Deadlock**                                | Two or more processes waiting indefinitely for each other to release resources.                         |
| **Livelock**                                | Processes continuously retry but never make useful progress.                                            |
| **Starvation**                              | A process waits indefinitely because resources are continually allocated to others.                     |
| **Thread Safety**                           | The property of code behaving correctly when accessed by multiple threads concurrently.                 |
| **Data Management**                         | The practice of storing, organizing, protecting, and maintaining data throughout its lifecycle.         |
| **Normalization**                           | Organizing relational data to reduce redundancy and improve integrity.                                  |
| **Denormalization**                         | Introducing controlled redundancy to improve read performance.                                          |
| **Index**                                   | A data structure that accelerates data retrieval operations.                                            |
| **Partitioning**                            | Dividing a large dataset into smaller logical segments within the same database.                        |
| **Sharding**                                | Distributing data across multiple independent databases to improve scalability.                         |
| **Caching**                                 | Storing frequently accessed data closer to consumers to reduce latency.                                 |
| **Cache Hit**                               | A request served directly from the cache.                                                               |
| **Cache Miss**                              | A request not found in the cache, requiring retrieval from the original data source.                    |
| **Cache Hit Ratio**                         | Percentage of requests successfully served from cache.                                                  |
| **TTL (Time To Live)**                      | The duration after which cached data expires.                                                           |
| **Cache Eviction**                          | Removing entries from a cache when space is needed or data expires.                                     |
| **Messaging**                               | Communication between systems through messages rather than direct synchronous calls.                    |
| **Message Broker**                          | Middleware responsible for routing, storing, and delivering messages between producers and consumers.   |
| **Producer**                                | A component that publishes messages.                                                                    |
| **Consumer**                                | A component that processes received messages.                                                           |
| **Queue**                                   | A messaging structure where each message is typically consumed by one consumer.                         |
| **Topic**                                   | A publish-subscribe channel where multiple consumers can receive the same message.                      |
| **Event**                                   | A record indicating that something significant has occurred within the system.                          |
| **Event-Driven Architecture (EDA)**         | An architecture where components communicate primarily through events.                                  |
| **Publish-Subscribe (Pub/Sub)**             | A messaging model where publishers and subscribers are decoupled through topics.                        |
| **API (Application Programming Interface)** | A contract enabling software components to communicate.                                                 |
| **REST**                                    | An architectural style for stateless web APIs using HTTP resources.                                     |
| **gRPC**                                    | A high-performance RPC framework using Protocol Buffers and HTTP/2.                                     |
| **RPC (Remote Procedure Call)**             | Invoking a function on another system as if it were local.                                              |
| **Idempotency**                             | Repeating the same request produces the same result without unintended side effects.                    |
| **API Versioning**                          | Managing API evolution while preserving compatibility with existing clients.                            |
| **Backward Compatibility**                  | Ensuring existing clients continue working after system changes.                                        |
| **Authentication**                          | Verifying the identity of a user or system.                                                             |
| **Authorization**                           | Determining what an authenticated user or system is allowed to do.                                      |
| **Least Privilege**                         | Granting only the minimum permissions required to perform a task.                                       |
| **Encryption**                              | Converting data into an unreadable form that requires a key to decrypt.                                 |
| **TLS (Transport Layer Security)**          | A protocol securing network communication through encryption and authentication.                        |
| **Observability**                           | The ability to understand a system's internal state using telemetry data.                               |
| **Logs**                                    | Timestamped records describing events within a system.                                                  |
| **Metrics**                                 | Numerical measurements collected over time to monitor system health.                                    |
| **Traces**                                  | Records following a request across multiple services to visualize end-to-end execution.                 |
| **Distributed Tracing**                     | Tracking a request as it flows through multiple distributed services.                                   |
| **Telemetry**                               | Collective operational data including logs, metrics, traces, and events.                                |
| **Maintainability**                         | The ease with which a system can be understood, modified, tested, and evolved.                          |
| **Technical Debt**                          | The future cost of choosing a faster or simpler solution instead of a more maintainable one.            |
| **Refactoring**                             | Improving internal code structure without changing external behavior.                                   |
| **Modularity**                              | Organizing a system into independent, cohesive components with clear responsibilities.                  |
| **Coupling**                                | The degree of dependency between software components. Lower coupling improves maintainability.          |
| **Cohesion**                                | The degree to which the responsibilities within a module belong together. Higher cohesion is preferred. |
| **DRY (Don't Repeat Yourself)**             | Every piece of business knowledge or logic should exist in only one authoritative place.                |
| **KISS (Keep It Simple, Stupid)**           | Prefer the simplest solution that satisfies business requirements.                                      |
| **YAGNI (You Aren't Gonna Need It)**        | Avoid implementing functionality until there is a real business need.                                   |
| **SOLID Principles**                        | Five object-oriented design principles that improve flexibility and maintainability.                    |
| **Dependency Injection (DI)**               | Supplying dependencies from outside a component rather than creating them internally.                   |
| **Hexagonal Architecture**                  | An architecture isolating business logic from external technologies through ports and adapters.         |
| **Clean Architecture**                      | An architecture where dependencies always point toward business rules rather than frameworks.           |
| **Domain-Driven Design (DDD)**              | Designing software around business domains and ubiquitous language.                                     |
| **Feature Flag**                            | A runtime switch enabling or disabling features without redeployment.                                   |
| **CI (Continuous Integration)**             | Frequently integrating code changes with automated builds and tests.                                    |
| **CD (Continuous Delivery)**                | Automatically preparing software for production deployment after successful validation.                 |
| **Infrastructure as Code (IaC)**            | Managing infrastructure through version-controlled code rather than manual configuration.               |
| **Connection Pool**                         | A reusable pool of connections that avoids repeatedly creating expensive connections.                   |
| **Compression**                             | Reducing data size to improve network transfer efficiency.                                              |
| **Batching**                                | Combining multiple operations into one request to reduce overhead.                                      |
| **Read Replica**                            | A replicated database instance dedicated primarily to serving read requests.                            |
| **CDN (Content Delivery Network)**          | A globally distributed network that serves content closer to end users.                                 |
| **Edge Computing**                          | Processing requests near users rather than exclusively in centralized data centers.                     |
| **Load Balancer**                           | A component that distributes incoming traffic across multiple servers.                                  |
| **Backpressure**                            | A mechanism preventing producers from overwhelming slower consumers.                                    |
| **Flow Control**                            | Regulating the rate of data transmission between communicating systems.                                 |
| **Autoscaling**                             | Automatically adjusting computing resources based on workload.                                          |
| **Percentile (P95/P99)**                    | The latency below which 95% or 99% of requests complete.                                                |
| **Tail Latency**                            | The latency experienced by the slowest requests, typically measured using P95, P99, or P99.9.           |
| **Little's Law**                            | A performance law relating throughput, latency, and the number of requests in a system.                 |
| **Capacity Planning**                       | Estimating future resource requirements based on expected growth and workload.                          |
| **Load Testing**                            | Evaluating system behavior under expected production traffic.                                           |
| **Stress Testing**                          | Evaluating system behavior beyond expected capacity to identify failure points.                         |
| **Spike Testing**                           | Evaluating system behavior under sudden traffic increases.                                              |
| **Soak Testing**                            | Evaluating long-term system stability under sustained load.                                             |

---
