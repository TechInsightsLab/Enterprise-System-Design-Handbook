
# 9. CAP & PACELC in System Design Interviews

One of the reasons.

CAP and PACELC.

Are asked so frequently.

In interviews.

Is because.

They test.

Whether you understand.

Architectural trade-offs.

Not.

Whether you memorized.

Definitions.

---

# Interview Reality

Interviewer.

Rarely asks.

> "Define CAP Theorem."

Instead.

They ask.

Questions like.

- Why did you choose Cassandra?
- Why not PostgreSQL?
- Why does DynamoDB scale so well?
- Why does CockroachDB have higher write latency?
- Why doesn't Kafka guarantee global ordering?
- Why can't every database provide strong consistency?

These are.

CAP.

And.

PACELC.

Questions.

In disguise.

---

# Architect's Thought Process

Before selecting.

A database.

Always ask.

```text
Business Requirements

↓

Failure Behavior

↓

Consistency Requirements

↓

Latency Requirements

↓

Database Selection
```

Never.

Start.

With technology.

---

# Interview Framework

Whenever.

An interviewer asks.

"What database would you choose?"

Think.

In this order.

```text
Business

↓

Data

↓

Traffic

↓

Failures

↓

CAP

↓

PACELC

↓

Technology
```

Not.

The reverse.

---

# Scenario 1

## Banking System

Requirement.

```text
Money

Cannot

Disappear
```

Question.

Network partition occurs.

Should writes continue?

Answer.

No.

Consistency.

Is mandatory.

---

CAP.

```text
CP
```

PACELC.

```text
PC / EC
```

Possible technologies.

- CockroachDB
- Spanner
- PostgreSQL + synchronous replication
- FoundationDB

---

# Interview Answer

> "In a banking system, returning stale balances or allowing conflicting transactions has severe financial consequences. I would therefore prioritize consistency over availability during partitions and accept higher write latency during normal operation."

That answer.

Demonstrates.

Architectural reasoning.

---

# Scenario 2

## Instagram

Requirement.

```text
Users

Must

Always

Upload Photos
```

Partition occurs.

Should uploads stop?

No.

---

CAP.

```text
AP
```

PACELC.

```text
PA / EL
```

Possible technologies.

- Cassandra
- DynamoDB
- Riak

---

Interview Answer.

> "Temporary inconsistency is acceptable because a delayed profile picture update has low business impact. User experience and availability are more valuable."

---

# Scenario 3

## Product Catalog

Requirement.

Customers browse.

Millions.

Of products.

Inventory.

Can lag.

By.

A few seconds.

---

Choice.

Usually.

```text
AP

↓

PA / EL
```

Because.

Fast browsing.

Is more important.

Than immediate.

Inventory synchronization.

---

# Scenario 4

## Airline Reservation

Requirement.

```text
Never

Sell

Seat 18A

Twice
```

Partition occurs.

Should booking continue?

No.

---

Choice.

```text
CP

↓

PC / EC
```

Correctness.

Is mandatory.

---

# Scenario 5

## Chat Application

Requirement.

Messages.

Should arrive.

Quickly.

Occasional delay.

Is acceptable.

---

Choice.

Often.

```text
AP

↓

PA / EL
```

Combined with.

FIFO ordering.

Within conversations.

---

# Decision Framework

When designing.

Any distributed system.

Ask yourself.

---

## Question 1

What happens.

If.

Data becomes stale?

---

If answer is.

```text
Nothing serious
```

Availability.

May be preferred.

---

If answer is.

```text
Financial loss

Legal issue

Safety issue
```

Consistency.

Becomes mandatory.

---

## Question 2

Can users.

Retry later?

---

If yes.

Temporary unavailability.

May be acceptable.

---

If no.

Availability.

Becomes more valuable.

---

## Question 3

How expensive.

Is coordination?

---

Global consensus.

Adds.

Latency.

---

Question.

Does the business.

Need.

Global consensus?

---

## Question 4

How long.

Can inconsistency.

Exist?

---

Milliseconds?

Seconds?

Minutes?

Never?

---

This determines.

The required.

Consistency model.

---

# Decision Tree

```text
Can stale data cause serious damage?

        │

     Yes │ No

        │

        ▼

       CP

        │

Need Global Transactions?

        │

   Yes      No

    │        │

 PC/EC     PC/EL

---------------------------

If stale data is acceptable

↓

AP

↓

Latency Critical?

↓

Yes → PA/EL

↓

No → PA/EC
```

---

# Common System Design Mistakes

## Mistake 1

Choosing.

Strong consistency.

For every system.

---

Problem.

Unnecessary latency.

Lower scalability.

Higher cost.

---

Better.

Choose.

The weakest guarantee.

That satisfies.

Business correctness.

---

## Mistake 2

Ignoring.

Business requirements.

---

Wrong approach.

```text
I always use Cassandra.
```

---

Correct approach.

```text
For this business.

Availability.

Is more important.

Therefore.

Cassandra.

Fits well.
```

---

## Mistake 3

Believing.

One database.

Solves.

Every problem.

---

Reality.

Large systems.

Often use.

Multiple databases.

---

Example.

Amazon.

```text
Payments

↓

Strong Consistency

---------------------

Catalog

↓

Eventual Consistency

---------------------

Analytics

↓

Eventually Consistent

---------------------

Cache

↓

Redis
```

Every component.

Optimized.

Independently.

---

## Mistake 4

Ignoring.

Replication latency.

---

Global replication.

Is not free.

Every replica.

Adds.

Communication.

Synchronization.

Failure scenarios.

---

# Production Best Practices

## Design for Failure

Assume.

Network partitions.

Will occur.

Do not.

Treat them.

As edge cases.

---

## Understand Business Impact

Always quantify.

The cost.

Of.

- Stale data
- Rejected requests
- Lost writes
- Increased latency

Architecture.

Should optimize.

For business outcomes.

---

## Prefer Locality

Whenever possible.

Serve reads.

From.

The nearest replica.

Reduce latency.

Without sacrificing.

Required consistency.

---

## Make Consistency Configurable

Modern systems.

Often expose.

Configuration.

Instead of.

One fixed policy.

Examples.

- Read consistency
- Write consistency
- Quorum size
- Read preference

This allows.

Different workloads.

To choose.

Different guarantees.

---

## Measure Before Optimizing

Don't assume.

Consistency.

Is the bottleneck.

Measure.

- Latency
- Throughput
- Replication delay
- Conflict rate
- Availability

Then optimize.

---

# CAP vs PACELC Summary

| Topic | CAP | PACELC |
|--------|-----|---------|
| Introduced By | Eric Brewer | Daniel Abadi |
| Focus | Network Partitions | Partitions + Normal Operation |
| Main Trade-off | Consistency vs Availability | Consistency vs Availability vs Latency |
| Applies During | Failures | Failures and Healthy Systems |
| Best Use | Theoretical understanding | Practical architecture decisions |

---

# Chapter Summary

## CAP

You learned.

- Why distributed systems face unavoidable trade-offs.
- What Consistency means.
- What Availability means.
- Why Partition Tolerance is mandatory.
- Why "Choose Any Two" is a misconception.
- CP systems.
- AP systems.
- Real-world database examples.

---

## PACELC

You learned.

- Why CAP is incomplete.
- Why latency matters.
- PACELC classification.
- PA/EL.
- PA/EC.
- PC/EL.
- PC/EC.
- Modern database strategies.

---

# Quick Comparison

| Requirement | Recommended Choice |
|-------------|--------------------|
| Banking | CP / PC-EC |
| Stock Trading | CP / PC-EC |
| Airline Reservation | CP / PC-EC |
| Metadata Store | CP / PC-EC |
| Product Catalog | AP / PA-EL |
| Social Media | AP / PA-EL |
| News Feed | AP / PA-EL |
| Analytics | AP / PA-EL |
| Search | AP / PA-EL |

---

# Architecture Thinking

Whenever designing.

A distributed system.

Ask yourself.

### Business

- What happens if users see stale data?
- What happens if requests fail?
- Which is more expensive?
- How much inconsistency is acceptable?

---

### Technical

- Can replicas diverge temporarily?
- How quickly must replicas synchronize?
- Is global consensus necessary?
- Can reads be served locally?
- How much latency can users tolerate?
- What happens during a network partition?

---

### Operational

- How will conflicts be resolved?
- How will split-brain scenarios be handled?
- How will consistency levels be monitored?
- What metrics indicate replication lag?
- How will the system recover after a partition heals?

---

# Common Interview Questions

1. Why is "Choose Any Two" incorrect?
2. Why is Partition Tolerance considered mandatory?
3. Explain CAP using a banking example.
4. Explain CAP using a social media example.
5. Difference between CAP Consistency and ACID Consistency.
6. Difference between CAP and PACELC.
7. Why was PACELC introduced?
8. Which databases are typically CP?
9. Which databases are typically AP?
10. Why does CockroachDB have higher write latency?
11. Why does Cassandra scale horizontally so well?
12. Why doesn't Kafka provide global ordering?
13. How do quorum reads and writes affect CAP behavior?
14. Which consistency model would you choose for an airline reservation system and why?
15. Can one application use both CP and AP databases? Give an example.

---

# Architect's Perspective

CAP and PACELC are not about proving one database is better than another.

They are about understanding that **every distributed system is a series of business trade-offs**.

Experienced architects don't ask:

> **"Which database is the fastest?"**

or

> **"Which database is the most consistent?"**

Instead they ask:

> **"What level of consistency, availability, and latency does this business actually require?"**

That mindset distinguishes an architect from someone who simply knows database technologies.

---

**End of Chapter 20 – CAP Theorem & PACELC**

