
# 16. Lamport Logical Clocks

> **Lamport Logical Clocks assign logical timestamps to events so that if one event causally influences another, the timestamps preserve that relationship.**

Introduced by.

**Leslie Lamport**

In 1978.

Lamport Clocks.

Were the first practical solution.

For ordering events.

Without requiring.

Perfect clock synchronization.

---

# The Big Idea

Instead of synchronizing.

Physical clocks.

Each process.

Maintains.

A simple counter.

```text
Process A

Counter = 7

--------------------

Process B

Counter = 15

--------------------

Process C

Counter = 3
```

The counter.

Represents.

Logical Time.

Not wall-clock time.

---

# Core Principle

If.

Event A.

Caused.

Event B.

Then.

```text
Timestamp(A)

<

Timestamp(B)
```

This property.

Must always hold.

---

# Lamport Clock Rules

Lamport Clocks follow only **three simple rules**.

Everything else.

Emerges from them.

---

# Rule 1 — Internal Event

Whenever.

A process.

Performs.

An internal event.

Increment.

Its logical clock.

---

## Formula

```text
Clock = Clock + 1
```

---

## Example

Initially.

```text
Process A

Clock = 5
```

Internal event.

Occurs.

```text
Read Database
```

Clock becomes.

```text
6
```

---

## Timeline

```text
Clock = 5

↓

Internal Event

↓

Clock = 6
```

Simple.

---

# Rule 2 — Sending a Message

Before sending.

Any message.

Increment.

The logical clock.

Attach.

The timestamp.

To the message.

---

## Formula

```text
Clock = Clock + 1

↓

Send(Message, Clock)
```

---

## Example

Initially.

```text
Clock = 10
```

Send payment request.

```text
Clock = 11
```

Message contains.

```text
Payment Request

Timestamp = 11
```

---

# Timeline

```text
Clock = 10

↓

Increment

↓

11

↓

Send

(Message,11)
```

---

# Why Attach Timestamp?

The receiving process.

Needs.

To understand.

The logical order.

Of events.

Without timestamps.

Ordering.

Cannot be preserved.

---

# Rule 3 — Receiving a Message

This is.

The most important rule.

When receiving.

A message.

The process compares.

Its own clock.

With.

The received timestamp.

Then chooses.

The larger value.

Finally.

Adds one.

---

## Formula

```text
Clock

=

max(Local Clock,

Received Timestamp)

+

1
```

This guarantees.

Logical time.

Always moves forward.

---

# Example

Process A.

Sends.

```text
Timestamp = 15
```

Process B.

Currently has.

```text
Clock = 10
```

Calculation.

```text
max(10,15)

+

1

=

16
```

Process B.

Updates.

Its logical clock.

To.

```text
16
```

---

# Another Example

Process A.

Sends.

```text
Timestamp = 5
```

Process B.

Already has.

```text
Clock = 20
```

Calculation.

```text
max(20,5)

+

1

=

21
```

Clock continues.

Forward.

---

# Why max()?

Imagine.

Without max().

---

Process A.

```text
Timestamp

100
```

Process B.

```text
Clock

5
```

If Process B.

Simply added one.

Clock becomes.

```text
6
```

Logical time.

Moves backwards.

Relative to.

The received event.

Causality breaks.

---

Using.

```text
max()

+

1
```

Prevents this.

---

# Complete Algorithm

Every process.

Maintains.

One integer.

```text
Logical Clock
```

For every event.

```text
Internal Event

↓

Clock++

--------------------

Send Message

↓

Clock++

↓

Attach Timestamp

--------------------

Receive Message

↓

Clock

=

max(Local,

Received)

+

1
```

Only.

Three rules.

Entire algorithm.

---

# Worked Example

Two processes.

Communicate.

---

Initially.

```text
Process A

Clock = 0

--------------------

Process B

Clock = 0
```

---

### Step 1

Process A.

Internal event.

```text
Clock

1
```

---

### Step 2

Process A.

Sends message.

Increment.

```text
2
```

Message.

Carries.

```text
Timestamp = 2
```

---

### Step 3

Process B.

Receives message.

Current clock.

```text
0
```

Calculation.

```text
max(0,2)

+

1

=

3
```

Process B.

Clock.

Becomes.

```text
3
```

---

### Step 4

Process B.

Internal event.

Clock.

```text
4
```

---

### Timeline

```text
Process A                  Process B

0                          0

│

Internal

↓

1

│

Send

↓

2 ---------------------->

                           max(0,2)+1

                           ↓

                           3

                           │

                           Internal

                           ↓

                           4
```

Notice.

Time.

Always moves forward.

---

# Another Example

Three processes.

```text
A

B

C
```

---

Process A.

```text
Internal

↓

1

↓

Send

↓

2
```

---

Process B.

Receives.

```text
max(0,2)

+

1

=

3
```

---

Process B.

Sends.

```text
4
```

---

Process C.

Receives.

```text
max(0,4)

+

1

=

5
```

Timeline.

```text
A

1

↓

2 ------------>

                B

                3

                ↓

                4 ------------>

                               C

                               5
```

Ordering preserved.

---

# Why Lamport Clocks Work

Suppose.

Event A.

Caused.

Event B.

Then.

The message.

Carries.

A timestamp.

The receiver.

Updates.

Using.

```text
max()

+

1
```

Therefore.

```text
Timestamp(A)

<

Timestamp(B)
```

Always true.

---

# Important Property

Lamport proved.

If.

```text
A

→

B
```

(Happens-before)

Then.

```text
Lamport(A)

<

Lamport(B)
```

This preserves.

Causality.

---

# But Notice Carefully

The reverse.

Is **NOT** true.

Suppose.

```text
Timestamp(A)

=

5

Timestamp(B)

=

10
```

Does that mean.

A caused B?

No.

Maybe.

Maybe not.

This limitation.

Is extremely important.

---

# Example

Two independent servers.

```text
Server A

Timestamp = 5

---------------------

Server B

Timestamp = 10
```

No communication.

Occurred.

The timestamps.

Still differ.

Lamport.

Cannot distinguish.

Concurrency.

---

# Advantages

Lamport Clocks are.

Simple.

Fast.

Require.

Very little memory.

Only.

One integer.

Per process.

---

# Advantages Summary

- Very simple algorithm
- No clock synchronization required
- Preserves causal ordering
- Minimal storage
- Low communication overhead
- Easy implementation

---

# Limitations

Lamport Clocks.

Cannot answer.

Whether.

Two events.

Occurred independently.

They only guarantee.

One direction.

```text
Cause

↓

Later Timestamp
```

Not.

```text
Later Timestamp

↓

Cause
```

---

# Example

```text
Server A

Timestamp 8

---------------------

Server B

Timestamp 10
```

No communication.

Between them.

Lamport.

Cannot determine.

Whether.

The events.

Are concurrent.

---

# Why This Matters

Imagine.

Two customers.

Edit.

Different documents.

At the same time.

Lamport timestamps.

May differ.

But.

Neither edit.

Depends.

On the other.

We need.

A richer algorithm.

To detect.

Concurrency.

---

# This Leads to Vector Clocks

Lamport Clocks answer.

```text
Did A happen before B?
```

Sometimes.

---

Vector Clocks answer.

```text
Did A happen before B?

Or

Were they concurrent?
```

We'll study.

Vector Clocks.

After understanding.

Happens-Before.

---

# Lamport Clock Summary

| Event | Action |
|--------|--------|
| Internal Event | Increment local clock |
| Send Message | Increment clock and attach timestamp |
| Receive Message | max(local, received) + 1 |

---

# Common Misconceptions

### Misconception 1

> Lamport clocks synchronize physical clocks.

False.

They ignore.

Physical time.

Completely.

---

### Misconception 2

> Lamport timestamps represent seconds.

False.

They are.

Logical counters.

---

### Misconception 3

> Higher Lamport timestamp always means later in real time.

False.

It means.

Later.

In logical ordering.

---

### Misconception 4

> Lamport clocks detect concurrent events.

False.

That requires.

Vector Clocks.

---

# Architect's Perspective

Lamport Clocks were a breakthrough because they changed the objective of distributed systems.

Instead of attempting the impossible task of making every machine agree on the exact time, they asked a simpler question:

> **Can we preserve causal relationships between events?**

The answer was yes.

With just a single counter per process.

This elegant idea forms the conceptual foundation for:

- Event Ordering
- Distributed Databases
- Replication
- Consensus Algorithms
- Distributed Transactions

However, Lamport Clocks have an important limitation:

They cannot distinguish between **causally related events** and **independent concurrent events**.

That limitation motivated the invention of **Vector Clocks**, which we'll study after understanding the **Happens-Before Relationship**.

---

**End of Part 6**

The next part will cover:

- **Happens-Before Relationship (→)**
- **Causality**
- **Concurrent Events**
- **Partial Ordering**
- **Total Ordering**
- **Worked Examples**
- **Why Happens-Before is the Foundation of Distributed Systems**
