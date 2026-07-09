
# 1. What is Time?

Time seems.

Simple.

Everyone uses it.

Every day.

But in distributed systems.

Time is one of the most complex concepts.

---

# Human Definition

For humans.

Time answers questions like.

- When did it happen?
- How long did it take?
- What happened first?
- What happens next?

---

# Computer Definition

For computers.

Time is used to.

- Timestamp events
- Schedule jobs
- Measure duration
- Detect timeouts
- Expire cache
- Order transactions
- Synchronize systems
- Generate logs

Almost every software system.

Depends on time.

---

# Example

Customer logs in.

```text
09:00:00

↓

Login Recorded

↓

JWT Expiration

↓

09:30:00
```

Without time.

Authentication.

Would not work.

---

# Another Example

Redis Cache.

```text
TTL

↓

5 Minutes

↓

Automatic Expiration
```

Time determines.

When data expires.

---

# Example

Payment.

```text
Created

↓

Timeout

↓

Cancelled

(After 10 Minutes)
```

Again.

Time.

Controls business logic.

---

# Time in Distributed Systems

Unlike humans.

Distributed systems.

Do not share.

One universal clock.

Instead.

Every machine.

Measures time.

Independently.

---

# Architecture

```text
Machine A

↓

Clock A

----------------------

Machine B

↓

Clock B

----------------------

Machine C

↓

Clock C
```

Each clock.

Runs independently.

---

# Why Computers Need Clocks

Without clocks.

Computers cannot.

- Measure duration
- Schedule execution
- Order events
- Detect failures
- Synchronize replicas

Time is fundamental.

To computing.

---

# 2. How Computers Keep Time

Every computer.

Contains.

A physical clock.

Unlike software.

It continues running.

Even when.

The computer is powered off.

---

# Clock Architecture

```text
Quartz Crystal

↓

Hardware Clock

↓

Operating System Clock

↓

Applications
```

Time flows.

From hardware.

Into software.

---

# Components

Every computer typically has.

- Quartz Crystal Oscillator
- Real-Time Clock (RTC)
- CPU Timer
- Operating System Clock

Each has.

Different responsibilities.

---

# 3. Hardware Clock (RTC)

> **The Hardware Clock (Real-Time Clock) is a battery-powered clock that keeps track of time even when the computer is turned off.**

It is.

Part of.

The motherboard.

---

# Example

Laptop.

Powered off.

For.

Three days.

Powered on.

Clock still knows.

The current date.

Because.

RTC continued running.

---

# Architecture

```text
Battery

↓

RTC Chip

↓

Current Time
```

---

# Responsibilities

The hardware clock stores.

- Date
- Time
- Seconds
- Minutes
- Hours

When the OS starts.

It reads.

The RTC.

---

# Why Battery?

If power disappears.

RAM loses data.

But.

The RTC battery.

Continues powering.

The clock.

---

# Limitations

Hardware clocks.

Are not perfectly accurate.

They slowly drift.

We'll discuss.

Clock drift shortly.

---

# 4. System Clock

> **The System Clock is the software clock maintained by the operating system.**

Applications.

Do not usually read.

The hardware clock.

Instead.

They read.

The system clock.

---

# Architecture

```text
Hardware Clock

↓

Operating System

↓

System Clock

↓

Applications
```

---

# Example

Java.

```java
Instant.now()

LocalDateTime.now()

System.currentTimeMillis()
```

These APIs.

Read.

The system clock.

Not directly.

The hardware clock.

---

# Why Separate Clocks?

Reading hardware.

Every time.

Would be expensive.

The OS.

Keeps.

A fast.

In-memory clock.

---

# System Clock Updates

The operating system.

Continuously updates.

Its software clock.

Using.

- Hardware timers
- CPU interrupts
- Time synchronization

---

# Architect's Perspective

Applications trust.

The operating system clock.

Not the RTC.

---

# 5. UTC (Coordinated Universal Time)

> **UTC is the global time standard used by computers worldwide.**

UTC.

Is not.

A time zone.

It is.

The reference.

Against which.

All time zones.

Are measured.

---

# Example

```text
UTC

12:00 PM
```

India.

```text
UTC +05:30

↓

5:30 PM
```

London.

```text
UTC +00:00
```

New York.

```text
UTC -05:00
```

(Offset varies with daylight saving.)

---

# Why UTC?

Imagine.

Every system.

Stored.

Local time.

Comparing logs.

Would become difficult.

---

Instead.

Servers store.

UTC.

Display.

Local time.

To users.

---

# Best Practice

Always.

Store timestamps.

In UTC.

Convert.

Only when.

Displaying.

To users.

---

# Example

Database.

```text
2026-08-15T12:00:00Z
```

User.

In India.

Sees.

```text
17:30 IST
```

User.

In Germany.

Sees.

```text
14:00 CEST
```

Same timestamp.

Different presentation.

---

# Architect's Perspective

Store UTC.

Display Local Time.

Never the reverse.

---

# 6. Epoch Time (Unix Timestamp)

> **Epoch Time represents the number of seconds or milliseconds that have elapsed since January 1, 1970 00:00:00 UTC.**

This date is called.

The Unix Epoch.

---

# Example

```text
1970-01-01

00:00:00 UTC

↓

0
```

One second later.

```text
1
```

One hour later.

```text
3600
```

---

# Java Example

```java
System.currentTimeMillis()
```

Returns.

Milliseconds.

Since.

Unix Epoch.

---

# Why Epoch?

Computers prefer.

Numbers.

Rather than.

Formatted dates.

Numbers.

Are easier.

To compare.

Store.

Transmit.

---

# Example

Instead of.

```text
15 Aug 2026

5:30 PM
```

Store.

```text
1786815000000
```

---

# Architect's Perspective

Epoch timestamps.

Simplify.

Storage.

Sorting.

Comparison.

---

# 7. Time Zones

A time zone.

Represents.

The local time.

Of a geographic region.

---

# Example

```text
UTC

↓

India

UTC+05:30

↓

Japan

UTC+09:00

↓

California

UTC-08:00
```

---

# Why Time Zones Matter

Global applications.

Serve users.

Across continents.

Every user.

Expects.

Local time.

---

# Example

Meeting.

```text
UTC

10:00
```

India.

```text
15:30
```

Germany.

```text
12:00
```

Same meeting.

Different local times.

---

# Best Practice

Internally.

Use UTC.

Externally.

Display local time.

---

# 8. Network Time Protocol (NTP)

> **NTP is a protocol that synchronizes computer clocks with highly accurate time servers.**

Since.

Hardware clocks.

Drift.

Computers periodically.

Synchronize.

Their clocks.

---

# Architecture

```text
GPS

↓

Atomic Clock

↓

NTP Server

↓

Server A

↓

Server B

↓

Server C
```

All machines.

Synchronize.

With trusted.

Time sources.

---

# Why NTP Exists

Without synchronization.

Servers.

Would gradually.

Drift apart.

Leading to.

Incorrect timestamps.

---

# Example

Server A.

```text
10:00:00
```

Server B.

```text
09:59:54
```

Difference.

6 seconds.

NTP.

Gradually adjusts.

The clocks.

---

# Important

NTP.

Does **not** make.

All clocks.

Perfectly identical.

It reduces.

Clock differences.

---

# How NTP Works (Simplified)

1. Client sends a request to an NTP server.
2. NTP server replies with its current time.
3. The client estimates:
   - Network delay
   - Clock offset
4. The operating system gradually adjusts its system clock.

Rather than jumping time abruptly, NTP typically **slews** the clock (slightly speeds it up or slows it down) to avoid breaking running applications.

---

# Enterprise Example

Cloud servers.

In AWS.

Azure.

Google Cloud.

Usually synchronize.

Using internal.

Highly accurate.

NTP services.

This keeps.

Most servers.

Within milliseconds.

Of UTC.

---

# Common Misconceptions

### Misconception 1

> Every computer has exactly the same time.

False.

Each computer has.

Its own clock.

---

### Misconception 2

> The hardware clock is perfectly accurate.

False.

All physical clocks.

Drift.

Over time.

---

### Misconception 3

> UTC and GMT are identical.

For most software engineering purposes.

They are treated similarly.

However.

UTC is the international time standard used by modern computer systems.

---

### Misconception 4

> NTP guarantees identical clocks.

False.

NTP reduces.

Clock differences.

It cannot eliminate.

Network latency.

Hardware imperfections.

Or transient delays.

---

# Architect's Perspective

Many production bugs originate from incorrect assumptions about time.

Experienced architects follow a few simple rules:

- Store timestamps in **UTC**.
- Convert to local time only for presentation.
- Never rely on perfectly synchronized clocks.
- Use NTP to minimize drift, but assume small differences always exist.
- Remember that **physical time is only an approximation**.

The next section will explain **Clock Drift** and **Clock Skew**—the two fundamental reasons why distributed systems cannot rely solely on physical timestamps.

---

**End of Part 2**

The next part will cover:

- **Clock Drift**
- **Clock Skew**
- **Sources of Clock Inaccuracy**
- **Network Delay**
- **Leap Seconds**
- **Why Physical Clocks Cannot Guarantee Event Ordering**
- **Production Examples**
