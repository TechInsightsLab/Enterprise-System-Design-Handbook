
# 9. Clock Drift

> **Clock Drift is the gradual difference in the speed at which two clocks run over time.**

This is one of the most fundamental concepts.

In distributed systems.

No physical clock.

Runs perfectly.

Every clock.

Runs.

Slightly faster.

Or slower.

Than others.

---

# Simple Definition

Imagine.

Two identical watches.

Purchased.

On the same day.

Initially.

```text
Watch A

12:00:00

-----------------------

Watch B

12:00:00
```

Perfectly synchronized.

---

After one month.

```text
Watch A

12:00:01

-----------------------

Watch B

11:59:58
```

Neither watch.

Is broken.

They simply.

Run at slightly different speeds.

This is.

Clock Drift.

---

# Why Does Clock Drift Occur?

Computers use.

Quartz Crystal Oscillators.

To measure time.

Unfortunately.

Quartz crystals.

Are not perfect.

---

# Clock Architecture

```text
Quartz Crystal

↓

Oscillation

↓

Clock Pulse

↓

Operating System Clock
```

Tiny variations.

In oscillation frequency.

Cause drift.

---

# Factors Causing Drift

Many factors.

Influence clock accuracy.

- Temperature
- Manufacturing differences
- Aging hardware
- Voltage changes
- CPU frequency variation
- Crystal quality

No two crystals.

Behave exactly the same.

---

# Example

Suppose.

Server A.

Loses.

1 millisecond.

Every second.

Server B.

Gains.

2 milliseconds.

Every second.

Initially.

```text
10:00:00.000
```

After.

One hour.

Difference becomes.

Several seconds.

Without synchronization.

The difference.

Keeps growing.

---

# Visual Example

```text
Start

Server A

10:00:00

Server B

10:00:00

↓

After One Hour

Server A

10:59:58

Server B

11:00:03
```

Neither server.

Is intentionally wrong.

They drifted.

Naturally.

---

# Mathematical View

Assume.

Clock accuracy.

```
±20 ppm
```

(PPM = Parts Per Million)

Meaning.

A clock may drift.

20 microseconds.

Every second.

---

After.

One day.

That becomes.

Several seconds.

Enough.

To break.

Distributed algorithms.

---

# Real Production Example

Suppose.

A distributed database.

Uses timestamps.

To determine.

The latest write.

---

Server A.

```text
Customer Name

Updated

10:00:05
```

Server B.

```text
Customer Name

Updated

10:00:03
```

If.

Server B's clock.

Runs slower.

The newer update.

May appear.

Older.

Incorrect data.

May overwrite.

Correct data.

---

# Impact of Clock Drift

Clock drift affects.

Many systems.

Examples.

- Databases
- Kafka
- Kubernetes
- Redis
- Distributed Locks
- Token Expiration
- Cache TTL
- Scheduling

---

# Example

JWT Token.

Expires.

At.

```text
10:00:00
```

Server clock.

Runs.

30 seconds ahead.

Customer.

Gets logged out.

Too early.

---

# Another Example

Distributed Cache.

TTL.

```
5 Minutes
```

Server clock.

Runs slower.

Expired cache.

Remains available.

Longer.

Than intended.

---

# How Systems Handle Drift

Most production systems.

Periodically synchronize.

Using.

```text
GPS

↓

Atomic Clock

↓

NTP Server

↓

Application Servers
```

Synchronization.

Reduces drift.

It never.

Eliminates it.

---

# Architect's Perspective

Clock drift.

Is unavoidable.

Architectures should tolerate.

Small differences.

Instead of assuming.

Perfect synchronization.

---

# 10. Clock Skew

> **Clock Skew is the difference between the current times shown by two clocks at a specific moment.**

Many people confuse.

Drift.

And skew.

They are related.

But different.

---

# Simple Definition

Clock Drift.

Describes.

How clocks.

Become different.

Over time.

Clock Skew.

Measures.

The current difference.

Between them.

---

# Example

Server A.

```text
10:00:00
```

Server B.

```text
09:59:57
```

Current difference.

```
3 Seconds
```

That difference.

Is.

Clock Skew.

---

# Visual Example

```text
Server A

10:00:00

-------------------------

Server B

09:59:57

Clock Skew

=

3 Seconds
```

---

# Relationship

Clock Drift.

Creates.

Clock Skew.

```text
Clock Drift

↓

Time Passes

↓

Clock Skew
```

---

# Real Example

Imagine.

100 servers.

In a cluster.

Each drifts.

Slightly differently.

After several hours.

Every server.

Shows.

Different time.

That difference.

Is clock skew.

---

# Why Clock Skew Matters

Suppose.

Two users.

Update.

The same document.

---

Server A.

```text
Save

10:00:05
```

Server B.

```text
Save

10:00:03
```

Question.

Which save.

Occurred later?

Without synchronized clocks.

Nobody knows.

For certain.

---

# Example

Distributed Lock.

Lease expires.

At.

```text
10:00:00
```

Server clock.

Runs ahead.

Server believes.

Lock expired.

Another server.

Still believes.

Lock is active.

Now.

Two owners.

May exist.

This can lead to.

Split-brain behavior.

---

# Clock Skew in Databases

Imagine.

Replication.

```text
Primary

↓

Replica A

↓

Replica B
```

Updates.

Carry timestamps.

Replica clocks.

Disagree.

Conflict resolution.

Becomes difficult.

---

# Architect's Perspective

Many distributed databases.

Avoid relying solely.

On physical timestamps.

Instead.

They use.

Logical clocks.

Which we'll study.

Later.

---

# Clock Drift vs Clock Skew

| Clock Drift | Clock Skew |
|-------------|------------|
| Difference in clock speed | Difference in displayed time |
| Happens gradually | Observed at a specific moment |
| Cause | Effect |
| Continuous process | Current measurement |
| Reduced by synchronization | Measured after synchronization |

---

# Sources of Clock Inaccuracy

Clock inaccuracies arise from several sources.

---

## Quartz Crystal Imperfections

Every oscillator.

Runs.

Slightly differently.

---

## Temperature

Heat changes.

Crystal frequency.

Servers.

Running under heavy load.

May experience.

Different drift.

---

## Hardware Aging

Older crystals.

Become.

Less accurate.

Over time.

---

## Voltage Fluctuations

Changes.

In electrical conditions.

Can influence.

Oscillation.

---

## Virtual Machines

Virtual machines.

Do not own.

Physical hardware clocks.

They depend.

On the hypervisor.

This can introduce.

Additional inaccuracies.

---

## Containerized Environments

Containers.

Share.

The host kernel.

They inherit.

The host's system clock.

If the host time is wrong.

All containers.

See the same incorrect time.

---

# Enterprise Example

A Kubernetes cluster.

Running.

100 worker nodes.

One node.

Stops synchronizing.

With NTP.

After a few days.

Its clock.

Is.

45 seconds ahead.

Pods scheduled.

On that node.

Generate incorrect timestamps.

Debugging becomes difficult.

Because logs.

Appear.

Out of sequence.

---

# Measuring Clock Skew

Many monitoring systems.

Continuously measure.

Clock differences.

Typical production alert.

```text
Clock Skew

>

100 ms

↓

Alert
```

Large skew.

May indicate.

NTP problems.

Hardware issues.

Or virtualization problems.

---

# Common Misconceptions

### Misconception 1

> Clock Drift and Clock Skew are the same.

False.

Drift causes skew.

They are different concepts.

---

### Misconception 2

> NTP completely eliminates drift.

False.

It periodically corrects drift.

Small inaccuracies.

Always remain.

---

### Misconception 3

> Milliseconds don't matter.

For many business applications.

They may not.

For distributed databases.

Consensus algorithms.

Financial trading.

And leader election.

Milliseconds matter greatly.

---

### Misconception 4

> Containers have independent clocks.

False.

Containers inherit.

The host operating system's clock.

---

# Architect's Perspective

Clock drift and clock skew explain why **physical time alone cannot be trusted in distributed systems**.

Even when every server synchronizes with NTP:

- Small differences remain.
- Networks introduce delays.
- Hardware behaves differently.
- Time synchronization is never instantaneous.

Experienced architects therefore follow an important principle:

> **Use physical time for measuring duration and displaying timestamps. Use logical ordering for making distributed decisions.**

This realization leads directly to the next topic:

**Why physical clocks cannot reliably determine event ordering**, and why distributed systems introduced **Logical Clocks**, **Lamport Timestamps**, and **Vector Clocks**.

---

**End of Part 3**

The next part will cover:

- **Network Delay**
- **Message Delay**
- **Leap Seconds**
- **Why Physical Clocks Fail**
- **Physical Time vs Logical Time**
- **Introduction to Event Ordering**
- **Why We Need Logical Clocks**
