
# 5. What CAP Theorem Actually Says

This is the section.

Where most interview answers.

Go wrong.

Almost everyone says.

> "CAP means choose any two out of three."

Unfortunately.

That statement.

Is incorrect.

Let's understand.

What CAP actually says.

---

# The Famous CAP Triangle

Most books.

Show CAP.

Like this.

```text
           Consistency
                 ▲
                / \
               /   \
              /     \
             /       \
            /         \
Availability -------- Partition Tolerance
```

Many people conclude.

> Pick any two.

This interpretation.

Is incomplete.

And often misleading.

---

# The Formal Statement

Eric Brewer's theorem.

Later proven.

By Gilbert & Lynch.

States:

> **When a network partition occurs, a distributed system must choose between Consistency and Availability.**

Notice carefully.

The theorem says.

**When a partition occurs.**

Not.

"At all times."

---

# The Correct Formula

Instead of.

```text
Choose Any Two
```

Think of CAP as.

```text
IF

Network Partition

Occurs

↓

Choose

Consistency

OR

Availability
```

That is the theorem.

---

# Why "Choose Any Two" Is Wrong

Imagine.

One database.

Running.

On one machine.

```text
Application

↓

Database
```

There is.

No network partition.

CAP.

Doesn't even apply.

You have.

- Consistency ✅
- Availability ✅

No trade-off.

---

Now.

Add another node.

```text
Node A

↓

Network

↓

Node B
```

Only now.

Can partitions occur.

Only now.

CAP becomes relevant.

---

# The Real Decision

Suppose.

The network breaks.

```text
Node A

❌

Node B
```

Should.

Both nodes.

Continue accepting writes?

Or.

Should one stop?

You cannot have both.

Perfect consistency.

And.

Perfect availability.

During the partition.

---

# Scenario 1 — Choose Consistency (CP)

Suppose.

Two replicas.

Lose communication.

```text
Replica A

❌

Replica B
```

Customer updates.

Profile.

On Replica A.

Replica B.

Cannot verify.

The update.

To preserve.

Consistency.

Replica B.

Rejects.

The request.

---

# Timeline

```text
Write Request

↓

Partition

↓

Replica Cannot Verify

↓

Reject Request
```

Consistency preserved.

Availability reduced.

---

# Business Example

Bank Transfer.

```text
Primary

↓

Network Failure

↓

Replica
```

Rather than.

Risk.

Incorrect balances.

The bank.

Temporarily rejects.

Transactions.

Correctness.

Is more important.

Than availability.

---

# Characteristics of CP Systems

CP systems.

Prioritize.

```text
Correct Data

↓

Even If

↓

Some Requests Fail
```

---

# Advantages

- Strong consistency
- No stale reads
- No conflicting writes
- Better correctness

---

# Disadvantages

- Reduced availability
- Users may receive errors
- Requests may timeout

---

# Typical Use Cases

- Banking
- Financial Trading
- Inventory
- Airline Reservations
- Metadata Services
- Distributed Locks

---

# Scenario 2 — Choose Availability (AP)

Suppose.

The partition occurs.

Again.

```text
Replica A

❌

Replica B
```

Instead of rejecting.

Requests.

Both replicas.

Continue serving.

Clients.

---

# Timeline

```text
Write Request

↓

Partition

↓

Replica Accepts Write

↓

Replication Later
```

Availability preserved.

Consistency delayed.

---

# Business Example

Social Media.

Customer changes.

Profile picture.

Replica A.

Shows.

New picture.

Replica B.

Still shows.

Old picture.

For a few seconds.

Acceptable.

---

# Characteristics of AP Systems

AP systems.

Prioritize.

```text
Always Respond

↓

Even If

↓

Data Is Temporarily Different
```

---

# Advantages

- High availability
- Better user experience
- Resilient during outages
- Excellent scalability

---

# Disadvantages

- Stale reads
- Conflicting updates
- Eventual consistency
- Conflict resolution required

---

# Typical Use Cases

- Social Networks
- Product Catalogs
- Recommendations
- Analytics
- Search Engines
- DNS

---

# Side-by-Side Comparison

## CP

```text
Partition

↓

Reject Request

↓

Consistency Preserved
```

---

## AP

```text
Partition

↓

Accept Request

↓

Synchronize Later
```

---

# Real Production Example

Imagine.

An e-commerce website.

Running.

Across two regions.

A partition occurs.

Customer A.

Purchases.

The last laptop.

---

### CP Approach

Region B.

Cannot verify inventory.

Rejects purchase.

Customer sees.

```text
Service Temporarily Unavailable
```

No overselling.

---

### AP Approach

Region B.

Accepts purchase.

Later.

Synchronization reveals.

Both regions.

Sold.

The same laptop.

Now.

The business.

Must resolve.

The conflict.

---

# Visual Comparison

### CP

```text
Client

↓

Replica

↓

Partition

↓

Reject

↓

Consistent
```

---

### AP

```text
Client

↓

Replica

↓

Partition

↓

Accept

↓

Eventually Synchronize
```

---

# What About CA?

People often ask.

"What about CA?"

Let's examine.

---

# CA (Consistency + Availability)

CA means.

```text
Always Consistent

+

Always Available
```

Sounds perfect.

---

But.

There is one problem.

It assumes.

No partitions.

---

# Example

Single Database.

```text
Application

↓

Database
```

No network.

No partition.

CA.

Is possible.

---

Distributed System.

```text
Node A

↓

Network

↓

Node B
```

The network.

Can fail.

Partition.

Occurs.

Now.

CA.

Cannot be maintained.

---

# Therefore

For real distributed systems.

Partition tolerance.

Is not optional.

Networks fail.

Eventually.

Every distributed system.

Must tolerate partitions.

---

# Important Clarification

This is why.

Many architects say.

CA systems.

Are largely theoretical.

In real distributed environments.

Because.

You cannot simply decide.

To ignore.

Network failures.

---

# Decision Matrix

| Partition Occurs? | Choice | Result |
|-------------------|--------|--------|
| No | Both Consistency & Availability | Possible |
| Yes | Consistency | Reject some requests |
| Yes | Availability | Return responses, possibly stale |

---

# Business Decision

Notice.

CAP.

Does not tell you.

Which choice.

Is correct.

Business requirements.

Make the decision.

---

# Banking

Wrong balance.

Is unacceptable.

Choose.

Consistency.

---

# Instagram

Showing.

An old profile picture.

For five seconds.

Is acceptable.

Choose.

Availability.

---

# Common Misconceptions

### Misconception 1

> CAP means choose any two.

False.

CAP says.

**When a partition occurs.**

Choose.

Consistency.

Or.

Availability.

---

### Misconception 2

> AP systems are inconsistent forever.

False.

Most AP systems.

Use.

Eventual consistency.

Replicas.

Synchronize later.

---

### Misconception 3

> CP systems are always unavailable.

False.

They remain available.

Until.

A partition forces.

A consistency decision.

---

### Misconception 4

> CA databases don't exist.

Single-node databases.

Can provide.

Consistency.

And availability.

CAP trade-offs.

Apply only.

To distributed systems.

---

# Architect's Decision Framework

When designing a distributed system, ask:

| Question | If Yes | Likely Choice |
|----------|---------|---------------|
| Is incorrect data unacceptable? | Banking, inventory, payments | CP |
| Is temporary stale data acceptable? | Social media, search, analytics | AP |
| Is the system distributed across unreliable networks? | Almost always | Partition tolerance is mandatory |
| Can users tolerate temporary errors? | Depends on business | CP may be acceptable |

---

# Architect's Perspective

The most important lesson from CAP is this:

> **Network partitions are not a possibility—they are an inevitability.**

Once a partition occurs, architects cannot preserve every desirable property simultaneously.

The decision is no longer technical.

It becomes a **business decision**:

- Is it more harmful to reject requests?
- Or more harmful to return stale or conflicting data?

Great architects understand that CAP is not about databases.

It is about aligning **technical trade-offs** with **business priorities**.

The next section will examine **real-world databases** and see how systems like Cassandra, MongoDB, ZooKeeper, Kafka, and DynamoDB make different CAP decisions.

---

**End of Part 3**

The next part will cover:

- **CP Systems**
- **AP Systems**
- **CA Systems**
- **Real Database Examples**
- **Cassandra**
- **MongoDB**
- **ZooKeeper**
- **Kafka**
- **DynamoDB**
- **CockroachDB**
