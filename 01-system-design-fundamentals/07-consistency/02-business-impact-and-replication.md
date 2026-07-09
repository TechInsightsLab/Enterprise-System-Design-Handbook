
# 8. Business Impact of Consistency

> **Consistency is ultimately a business concern, not a database feature.**

Most customers never ask:

- Is this database strongly consistent?
- Does the system use quorum reads?
- Is Raft implemented correctly?

Instead, they ask:

- Why was I charged twice?
- Why is my account balance incorrect?
- Why did my order disappear?
- Why did two customers buy the same item?

These are consistency problems.

---

# Business Perspective

Every business depends on trustworthy data.

Examples:

| Industry | Critical Data |
|----------|---------------|
| Banking | Account Balance |
| Retail | Inventory |
| Healthcare | Patient Records |
| Trading | Stock Orders |
| Travel | Seat Availability |
| Food Delivery | Order Status |
| Ride Sharing | Driver Assignment |

Incorrect data causes business failures.

---

# Revenue Impact

Imagine an e-commerce platform.

Only one PlayStation remains.

Two customers purchase simultaneously.

Inventory database temporarily disagrees.

```text
Inventory

1 Unit

↓

Customer A

Order Confirmed

↓

Customer B

Order Confirmed
```

Warehouse ships:

```
1 Console
```

Business now has:

- Refund cost
- Customer dissatisfaction
- Negative reviews
- Operational overhead

Poor consistency directly affects revenue.

---

# Banking Example

Suppose:

Balance:

```
₹100,000
```

Customer transfers:

```
₹90,000
```

Immediately afterwards,

another ATM reads an outdated replica.

Balance still appears:

```
₹100,000
```

Customer withdraws:

```
₹50,000
```

Final balance becomes impossible.

Strong consistency prevents such scenarios.

---

# Stock Trading

Milliseconds matter.

Example:

```text
Trader A

Places Order

↓

Exchange

↓

Replica Update Delayed

↓

Trader B

Reads Old Price
```

Trading decisions become incorrect.

Financial systems typically require very strong consistency.

---

# Healthcare

Doctor updates:

```
Drug Allergy
```

Hospital pharmacy reads stale information.

Patient receives incorrect medication.

Consistency becomes a safety requirement.

---

# Airline Reservation

One seat remains.

Two booking systems read different replicas.

Both reserve the seat.

Airport staff must manually resolve overbooking.

---

# Customer Trust

Consistency directly influences trust.

Example:

```text
Mobile Banking

↓

₹40,000

---------

ATM

↓

₹50,000

---------

Internet Banking

↓

₹40,000
```

Customers lose confidence even if the system eventually becomes correct.

---

# Regulatory Compliance

Industries requiring strict consistency include:

- Banking
- Healthcare
- Insurance
- Government
- Stock Exchanges

Incorrect data may violate:

- Financial regulations
- Medical regulations
- Audit requirements

---

# Business Cost of Inconsistency

| Problem | Business Impact |
|----------|-----------------|
| Duplicate Payment | Financial Loss |
| Double Booking | Compensation |
| Incorrect Inventory | Revenue Loss |
| Stale Reports | Wrong Decisions |
| Lost Orders | Customer Churn |
| Incorrect Balance | Compliance Risk |

---

# 9. Why Distributed Consistency Is Difficult

Consistency on one server is easy.

Consistency across thousands of servers is difficult.

---

# Single Database

```text
Application

↓

Database
```

One copy.

One truth.

---

# Distributed Database

```text
Application

↓

US

↓

Europe

↓

Asia
```

Now data exists in multiple places.

Questions emerge:

- Which copy is newest?
- Which copy is correct?
- What happens if the network fails?
- Which write wins?

---

# The Core Challenge

Imagine three replicas.

```text
Replica A

100

Replica B

100

Replica C

100
```

Customer updates:

```
150
```

Immediately:

```text
Replica A

150

Replica B

100

Replica C

100
```

Now answer:

Which value should customers read?

There is no universally correct answer.

Different systems choose different consistency guarantees.

---

# Network Latency

Suppose:

US

writes new value.

Europe receives update after:

```
120 ms
```

Asia receives update after:

```
280 ms
```

During those milliseconds,

customers observe different realities.

---

# Network Partition

Suppose communication fails.

```text
US

×

Europe

×

Asia
```

Each region continues accepting requests.

Eventually communication returns.

Now:

Each region contains different data.

How should conflicts be resolved?

---

# Concurrent Writes

Example:

Customer A

changes address.

Simultaneously,

Customer B

changes phone number.

Or worse,

both modify:

```
Shipping Address
```

Which update should survive?

---

# Clock Synchronization

Distributed systems have multiple clocks.

Example:

Server A:

```
10:00:01
```

Server B:

```
09:59:59
```

Which update happened first?

Physical clocks cannot always answer correctly.

---

# Message Loss

Messages may:

- Arrive late
- Arrive twice
- Arrive out of order
- Never arrive

Example:

```text
Write

↓

Lost

↓

Replica Never Updated
```

Applications must handle uncertainty.

---

# Node Failure

Suppose leader crashes after:

- Updating one replica
- Before updating another

Now replicas disagree.

Recovery becomes difficult.

---

# Simultaneous Failures

Imagine:

```text
Leader Failure

+

Network Partition

+

Heavy Traffic
```

Consistency decisions become significantly harder.

---

# 10. Data Replication

Replication exists primarily to improve:

- Availability
- Fault Tolerance
- Disaster Recovery
- Read Scalability

However,

replication introduces consistency challenges.

---

# Replication Overview

```text
Primary

↓

Replica A

↓

Replica B
```

Multiple copies improve resilience.

But now:

Every update must propagate.

---

# Synchronous Replication

```text
Write

↓

Primary

↓

Replica A

↓

Replica B

↓

Success
```

Advantages:

- Strong consistency
- No stale reads

Disadvantages:

- Higher latency
- Lower availability during failures

---

# Asynchronous Replication

```text
Write

↓

Primary

↓

Success

↓

Replica Later
```

Advantages:

- Low latency
- High availability

Disadvantages:

- Stale reads possible

---

# Semi-Synchronous Replication

```text
Primary

↓

One Replica

↓

Success

↓

Remaining Replicas Later
```

Balances latency and consistency.

---

# Replication Objectives

| Objective | Benefit |
|------------|----------|
| Availability | Survive failures |
| Scalability | Read from replicas |
| Disaster Recovery | Regional redundancy |
| Fault Tolerance | Automatic recovery |

Replication alone does **not** guarantee consistency.

---

# 11. Read Path

Understanding consistency begins with understanding reads.

---

# Read from Primary

```text
Client

↓

Primary

↓

Latest Data
```

Advantages:

- Strong consistency

Disadvantages:

- Higher load on primary

---

# Read from Replica

```text
Client

↓

Replica
```

Advantages:

- Better scalability

Risk:

Replica may be stale.

---

# Read Example

Customer updates profile.

Immediately:

```text
Read

↓

Replica

↓

Old Value
```

Eventually:

Replica catches up.

---

# Read Repair

Some databases detect stale replicas.

Example:

```text
Replica

Old Value

↓

Compare

↓

Latest Value

↓

Repair
```

Future reads become consistent.

---

# 12. Write Path

How writes propagate determines consistency guarantees.

---

# Primary Write

```text
Client

↓

Leader

↓

Replicas
```

Simple.

Common.

---

# Multi-Leader Write

```text
US Leader

Europe Leader

Asia Leader
```

Each region accepts writes.

Conflict resolution becomes necessary.

---

# Leaderless Write

```text
Node A

Node B

Node C
```

Client writes to multiple nodes.

Quorum determines success.

---

# Write Acknowledgement

Possible strategies:

Return success after:

- Primary only
- Majority
- All replicas

Each affects latency and consistency.

---

# 13. Consistency vs Integrity vs Isolation

These terms are frequently confused.

They are fundamentally different.

---

# Consistency

Question:

> **Do different users observe data according to the expected consistency model?**

Concern:

Distributed systems.

---

# Data Integrity

Question:

> **Is stored data valid?**

Example:

Negative account balance prohibited.

Primary concern:

Database constraints.

---

# Isolation

Question:

> **Can concurrent transactions interfere with each other?**

Primary concern:

Transactions.

---

# Example

Bank Transfer

Consistency:

Every replica eventually reflects the same balance.

---

Integrity:

Balance cannot become negative.

---

Isolation:

Two transfers should not corrupt each other.

---

# Comparison

| Property | Primary Concern |
|------------|----------------|
| Consistency | Distributed correctness |
| Integrity | Data validity |
| Isolation | Concurrent transactions |

---

# 14. Fundamental Consistency Problem

Every distributed database must answer one fundamental question.

```text
Client Updates Data

↓

When Should

Everyone Else

See It?
```

Possible answers:

Immediately

After majority agreement

Eventually

After synchronization

Each answer creates a different consistency model.

There is **no universally correct choice**.

The right decision depends entirely on:

- Business requirements
- Latency tolerance
- Availability requirements
- Cost of stale data

---

# Architect's Perspective

Experienced architects never begin with:

> "Which database should we use?"

They begin with:

- What is the business impact of stale data?
- Can users temporarily observe different values?
- How much latency is acceptable?
- Can conflicting updates occur?
- Which consistency model aligns with business risk?

Those answers determine the architecture—not the technology.

---

## Architecture Evolution Timeline

```text
Single Database

↓

Primary–Replica

↓

Multi-AZ Replication

↓

Multi-Region Replication

↓

Distributed Consensus

↓

Globally Consistent Databases

↓

Planet-Scale Distributed Systems
```
---

**End of Part 2**

The next section will cover:

- **Consistency Models**
- **Strong Consistency**
- **Weak Consistency**
- **Eventual Consistency**
- **Read-after-Write Consistency**
- **Monotonic Reads**
- **Monotonic Writes**
- **Session Consistency**
- **Causal Consistency**
- **Sequential Consistency**
- **Linearizability**
- **Consistency Model Decision Matrix**
