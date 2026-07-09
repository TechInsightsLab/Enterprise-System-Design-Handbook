
# Chapter 20 — CAP Theorem & PACELC

> **Part II – Distributed Systems**

---

# CAP Theorem & PACELC

| Attribute | Value |
|-----------|--------|
| **Version** | 2.0 |
| **Part** | II – Distributed Systems |
| **Chapter** | 20 |
| **Reading Time** | ~500–650 Minutes |
| **Difficulty** | Advanced |
| **Prerequisites** | Chapter 18 – Distributed Systems Fundamentals, Chapter 19 – Time, Clocks & Ordering |

---

> [!NOTE]
> **CAP Theorem is one of the most misunderstood topics in System Design.**
>
> Many engineers memorize "choose two out of three."
>
> Very few actually understand what the theorem says.
>
> Even fewer understand **PACELC**, which is what modern database architects use in practice.

---

# Why This Chapter Exists

Almost every distributed database.

Must answer.

One difficult question.

When.

The network fails.

Should the system.

Continue accepting requests?

Or.

Should it reject requests.

To avoid inconsistency?

There is.

No perfect answer.

CAP explains.

Why.

---

# Business Story

Imagine.

A banking application.

Running.

Across.

Two regions.

```text
Mumbai

↓

Primary Database

--------------------------

Singapore

↓

Replica Database
```

Normally.

Both databases.

Synchronize.

Continuously.

---

Now.

A network cable fails.

```text
Mumbai

❌

Singapore
```

Neither region.

Can communicate.

Question.

Customer deposits.

₹10,000.

In Mumbai.

Another customer.

Withdraws.

The same money.

In Singapore.

Should both operations.

Succeed?

If yes.

Balances.

May become inconsistent.

If no.

One customer.

Cannot use the bank.

This.

Is exactly.

The problem.

CAP addresses.

---

# Why CAP Matters

Every distributed system.

Eventually experiences.

Network failures.

Examples.

- Router failure
- Switch failure
- ISP outage
- Cloud region isolation
- Kubernetes networking issues
- Firewall misconfiguration
- DNS failures

Distributed systems.

Cannot pretend.

Networks are perfect.

---

# Real-World Systems

CAP influences.

The design of.

- Cassandra
- DynamoDB
- MongoDB
- CockroachDB
- Google Spanner
- ZooKeeper
- Kafka
- Redis Cluster
- Cosmos DB
- HBase

Every one.

Makes.

Different trade-offs.

---

# What You Will Learn

By the end.

Of this chapter.

You should understand.

- Why CAP exists.
- What Consistency means.
- What Availability means.
- What Partition Tolerance means.
- Why partitions are unavoidable.
- CP systems.
- AP systems.
- Why CA is largely theoretical.
- PACELC.
- Modern database design decisions.
- Interview misconceptions.
- Business trade-offs.

---

# Why CAP Exists

Suppose.

You have.

One database.

```text
Application

↓

Database
```

Simple.

No distributed problem.

---

Now.

Scale.

Across regions.

```text
Region A

↓

Database A

↓

Network

↓

Database B

↓

Region B
```

Immediately.

Questions appear.

- What if the network fails?
- Which copy is correct?
- Can writes continue?
- Should reads continue?
- Should users wait?

CAP.

Answers these questions.

---

# Single Machine vs Distributed System

## Single Machine

```text
Application

↓

Database
```

No partitions.

Simple consistency.

---

## Distributed Database

```text
Application

↓

Region A

↓

Database A

↓

Network

↓

Database B

↓

Region B
```

Network.

Can fail.

CAP begins.

---

# Historical Background

CAP Theorem.

Was proposed.

By.

**Eric Brewer**

In 2000.

At the.

PODC conference.

Later.

Formalized.

By.

**Gilbert & Lynch**

In 2002.

---

# The Core Question

When.

A network partition occurs.

Can a distributed system.

Guarantee.

- Correct data?
- Continuous availability?
- Both?

CAP says.

Not always.

---

# Why Architects Must Understand CAP

Every architecture decision.

Eventually becomes.

A CAP decision.

Examples.

Should.

An e-commerce website.

Allow.

Customers.

To continue shopping.

During a regional outage?

Or.

Stop all purchases.

Until synchronization finishes?

Different businesses.

Choose differently.

---

# The Three Properties

CAP consists of.

Three guarantees.

```text
Consistency

Availability

Partition Tolerance
```

The next sections.

Will examine.

Each one.

In depth.

Before discussing.

Their trade-offs.

---

# Common Misconceptions

### Misconception 1

> CAP is about performance.

False.

CAP discusses.

Behavior.

During.

Network partitions.

---

### Misconception 2

> CAP applies to every application.

False.

CAP applies.

To distributed systems.

Not.

Single-node applications.

---

### Misconception 3

> CAP says choose any two.

False.

This is.

The most common misconception.

We'll explain.

Why later.

---

### Misconception 4

> CAP is obsolete because cloud providers solved it.

False.

Cloud platforms.

Reduce failures.

They do not eliminate.

Network partitions.

---

# Architect's Perspective

CAP Theorem is not a database feature.

It is a **fundamental limitation of distributed computing**.

Just as physics limits the speed of light, CAP limits what distributed systems can guarantee during network failures.

The purpose of CAP is **not** to tell architects which database to choose.

Its purpose is to explain the trade-offs that every distributed system must eventually make.

Understanding those trade-offs is one of the defining skills of an experienced solution architect.

---

**End of Part 1**

The next part will cover:

- **Consistency (C)**
- **Availability (A)**
- **Partition Tolerance (P)**
- **Formal Definitions**
- **Business Examples**
- **Common Misunderstandings**
- **Visual Illustrations**
