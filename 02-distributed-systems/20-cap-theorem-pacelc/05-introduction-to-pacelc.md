
# 7. Why CAP Is Not Enough

CAP is.

One of the most important.

Theorems.

In distributed systems.

However.

Modern architects.

Rarely stop.

At CAP.

Why?

Because.

CAP discusses.

Only one situation.

When.

A network partition.

Occurs.

---

# The Missing Question

Imagine.

Your system.

Runs normally.

No failures.

No partitions.

Everything.

Is healthy.

Question.

Should the system.

Optimize for.

Consistency?

Or.

Latency?

CAP.

Doesn't answer.

This question.

---

# Example

Suppose.

Your application.

Runs.

Across.

Three regions.

```text
Mumbai

↓

Singapore

↓

Frankfurt
```

No network failure.

Everything.

Works perfectly.

Customer updates.

Profile.

Should the system.

Wait.

Until.

All regions.

Confirm.

The update?

Or.

Respond immediately.

And synchronize later?

CAP.

Has nothing to say.

Because.

There is.

No partition.

---

# The Reality

Most production systems.

Spend.

More than.

99.9%.

Of their lifetime.

Without.

Network partitions.

---

Question.

Should architects.

Ignore.

Performance.

Latency.

And user experience.

During.

Normal operation?

Obviously not.

---

# CAP's Blind Spot

CAP focuses.

Only on.

```text
Network Partition

↓

Consistency

vs

Availability
```

It ignores.

Everything else.

Such as.

- Latency
- User Experience
- Response Time
- Replication Delay
- Global Writes

---

# Business Example

Imagine.

Amazon.

Runs normally.

Customers.

Place orders.

No failures.

Should every order.

Wait.

For confirmation.

From.

Every replica.

Around the world?

Technically.

That improves consistency.

But.

Checkout latency.

May increase.

From.

50 ms.

To.

800 ms.

Customers.

May abandon.

Their carts.

---

# Real Architect Question

During normal operation.

Would you choose.

```text
Lower Latency

OR

Stronger Consistency
```

CAP.

Cannot answer.

---

# Enter PACELC

To address.

This limitation.

**Daniel Abadi**.

Proposed.

PACELC.

In 2010.

---

# What Does PACELC Mean?

PACELC extends.

CAP.

By considering.

Two situations.

Instead of one.

```text
IF

Partition

↓

Availability

OR

Consistency

ELSE

Latency

OR

Consistency
```

This becomes.

```text
PA / EL

C
```

Or.

More compactly.

```text
PACELC
```

---

# Breaking Down the Name

PACELC.

Looks complicated.

But it's simple.

---

## P

Partition.

Did a network partition occur?

---

## A

Availability.

If there is a partition.

Should the system.

Remain available?

---

## C

Consistency.

If there is a partition.

Should the system.

Prefer correctness?

---

## Else (EL)

Suppose.

No partition exists.

Should the system.

Optimize for.

Latency?

Or.

Consistency?

---

# PACELC Formula

```text
IF Partition

↓

Availability

OR

Consistency

ELSE

Latency

OR

Consistency
```

---

# Visual Representation

```text
                 Network Partition?

                    /          \

                  Yes          No

                  │             │

          Availability?     Latency?

               or               or

          Consistency      Consistency
```

Notice.

PACELC.

Adds.

One entirely new dimension.

Latency.

---

# Why Latency Matters

Imagine.

Your database.

Replicates.

Across.

Three continents.

Customer.

Updates.

Address.

Should the system.

Wait.

For.

Every continent.

To acknowledge.

The update?

---

Timeline.

```text
Client

↓

Write

↓

India

↓

Singapore

↓

Europe

↓

USA

↓

Response
```

Response.

May take.

Hundreds.

Of milliseconds.

---

Alternative.

```text
Client

↓

Write

↓

Local Replica

↓

Immediate Response

↓

Background Replication
```

Latency.

Drops dramatically.

---

# Consistency vs Latency

During normal operation.

Architects often choose between.

| Lower Latency | Stronger Consistency |
|--------------|----------------------|
| Faster responses | More synchronization |
| Better user experience | More coordination |
| Higher throughput | Higher correctness |
| Local acknowledgements | Global acknowledgements |

---

# Business Example

Google Search.

User.

Searches.

```text
Laptop
```

Would you wait.

Until.

Every replica.

Worldwide.

Agrees.

On.

Search ranking?

No.

Low latency.

Is more important.

---

Bank Transfer.

Would you wait.

For confirmation.

From all required replicas?

Yes.

Correctness.

Is more important.

---

# CAP vs PACELC

| CAP | PACELC |
|------|---------|
| Considers failures | Considers failures and normal operation |
| Partition only | Partition + no partition |
| Consistency vs Availability | Consistency vs Availability vs Latency |
| Simpler model | More practical model |
| Theoretical foundation | Practical architecture guidance |

---

# Example Timeline

## During Partition

```text
Network Failure

↓

Choose

Consistency

OR

Availability
```

---

## During Normal Operation

```text
Healthy Network

↓

Choose

Latency

OR

Consistency
```

PACELC.

Covers.

Both situations.

---

# Why PACELC Is More Useful

Most enterprise systems.

Experience.

Millions.

Of successful requests.

Every day.

Only.

Very few.

Partitions.

Therefore.

Architects.

Spend more time.

Optimizing.

Latency.

Than.

Handling partitions.

PACELC reflects.

This reality.

---

# Interview Tip

If asked.

> "Why was PACELC introduced?"

A strong answer is:

> **CAP only explains the trade-off during a network partition. PACELC extends this by also explaining the trade-off during normal operation, where architects often choose between lower latency and stronger consistency.**

---

# Common Misconceptions

### Misconception 1

> PACELC replaces CAP.

False.

PACELC extends.

CAP.

It includes.

CAP's partition trade-offs.

And adds.

Normal-operation trade-offs.

---

### Misconception 2

> Latency matters only during failures.

False.

Latency matters.

Primarily.

During normal operation.

---

### Misconception 3

> Every distributed database follows the same PACELC strategy.

False.

Different systems.

Choose different.

Latency.

Consistency.

Trade-offs.

---

### Misconception 4

> CAP is obsolete because PACELC exists.

False.

CAP remains.

The theoretical foundation.

PACELC builds upon it.

---

# Architect's Perspective

CAP teaches architects how to think during failures.

PACELC teaches architects how to think **every day**.

In real production environments, network partitions are relatively rare.

What users experience every second is:

- Response time.
- Throughput.
- Consistency guarantees.
- User-perceived latency.

These factors often have a greater impact on business success than the rare partition event.

That is why modern distributed databases are increasingly designed with **PACELC** in mind rather than CAP alone.

The next section will classify real-world databases using the PACELC model and show how systems like Cassandra, MongoDB, CockroachDB, and Spanner make different latency-versus-consistency decisions.

---

**End of Part 5**

The next part will cover:

- **PACELC Classification**
- **PA/EL Systems**
- **PA/EC Systems**
- **PC/EC Systems**
- **Real Database Examples**
- **Cassandra**
- **DynamoDB**
- **MongoDB**
- **CockroachDB**
- **Google Spanner**
