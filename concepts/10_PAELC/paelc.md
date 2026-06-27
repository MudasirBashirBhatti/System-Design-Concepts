# PACELC Theorem

## What is PACELC?

PACELC is an extension of the CAP Theorem.

CAP only explains what happens **during a network partition**.

PACELC explains:

> What happens during a partition (P) and what trade-off exists Else (E) when there is no partition.

---

## Full Form

    IF Partition (P)
        choose Availability (A)
        or Consistency (C)

    ELSE (E)
        choose Latency (L)
        or Consistency (C)

Hence:

    PACELC

---

# Why CAP Was Not Enough?

CAP says:

    Consistency
    Availability
    Partition Tolerance

During a network partition, you can choose only:

    CP
    or
    AP

But CAP does not explain:

    What happens when everything is working normally?

In real systems:

- Partitions are rare
- Normal operation is common

PACELC answers this missing part.

---

# The PACELC Formula

    IF Partition
        Availability or Consistency
    ELSE
        Latency or Consistency

Written as:

    PA/EL
    PA/EC
    PC/EL
    PC/EC

---

# Understanding the Two Decisions

## First Decision: During Partition

Imagine:

       Region A
          |
     Network Down
          |
       Region B

Now the regions cannot communicate.

You must choose:

### Consistency

or

### Availability

---

### Choose Consistency (C)

Reject requests if data cannot be guaranteed correct.

Example:

    Bank Account

Balance:

    $100

Two regions become disconnected.

To avoid inconsistent balances:

    Reject writes

Result:

    Correct Data ✅
    Less Available ❌

---

### Choose Availability (A)

Accept requests even if data may differ.

Example:

    Social Media Likes

Region A:

    101 Likes

Region B:

    103 Likes

Eventually they synchronize.

Result:

    Available ✅
    Temporarily Inconsistent ❌

---

# Second Decision: No Partition (Else)

Everything is healthy.

    Region A <----> Region B

Now choose:

### Low Latency

or

### Strong Consistency

---

## Strong Consistency (EC)

Every write must be synchronized.

Example:

    User Updates Profile
            |
        Region A
            |
        Region B
            |
      Success Response

Response is returned only after replicas acknowledge.

Benefits:

    Strong Consistency

Drawback:

    Higher Latency

---

## Low Latency (EL)

Respond immediately.

    User Update
         |
      Region A
         |
    Success Response

Replication happens later.

Benefits:

    Fast Response

Drawback:

    Temporary Inconsistency

---

# The Four PACELC Categories

## 1. PA/EL

Partition:
Choose Availability

Else:
Choose Low Latency

Characteristics:

- Always available
- Fast responses
- Eventual consistency

Diagram:

    Write
      |
    Node A
      |

Respond Immediately
|
Replicate Later

Examples:

- Dynamo-style systems
- Large-scale social media systems

---

## 2. PA/EC

Partition:
Choose Availability

Else:
Choose Consistency

Characteristics:

- Available during partition
- Strong consistency when healthy
- Higher normal latency

Less common.

---

## 3. PC/EL

Partition:
Choose Consistency

Else:
Choose Low Latency

Characteristics:

- Correct data during partition
- Optimized latency when healthy

Used when consistency is critical but performance is also important.

---

## 4. PC/EC

Partition:
Choose Consistency

Else:
Choose Consistency

Characteristics:

- Strong consistency always
- Higher latency
- May reject requests during failures

Diagram:

       Client
          |
      Primary
          |
     Replica 1
          |
     Replica 2
          |
      Success

Response waits for acknowledgments.

Examples:

- Traditional relational databases
- Financial systems

---

# Real-World Examples

## Banking System

Requirements:

- No double spending
- Accurate balances

Choice:

    PC/EC

Reason:

    Correctness > Availability
    Correctness > Latency

---

## Social Media Feed

Requirements:

- Fast responses
- High availability

Choice:

    PA/EL

Reason:

    Speed > Perfect Consistency

A post appearing 2 seconds later is acceptable.

---

## E-Commerce Product Catalog

Requirements:

- Fast reads
- High availability

Choice:

    PA/EL

Example:

    Product Stock = 100

One region may show:

    99

Another may show:

    100

Temporary inconsistency is acceptable.

---

## Payment Processing

Requirements:

- No duplicate transactions
- Correct balances

Choice:

    PC/EC

Consistency is mandatory.

---

# CAP vs PACELC

## CAP

Focuses only on partitions.

    Partition?
        |
    Availability
        or
    Consistency

---

## PACELC

Focuses on both scenarios.

    Partition?
        |
      A or C

    Else
        |
      L or C

PACELC is considered more practical because most systems spend most of their time in the "Else" state (no partition).

---

# Interview Example

Question:

    Why does Cassandra have low latency?

Answer:

    Cassandra is generally classified as PA/EL.

    During partitions it favors Availability.
    During normal operation it favors Low Latency over Strong Consistency.

This allows Cassandra to provide very fast reads and writes at massive scale.

---

# Memory Trick

    P  -> Partition
    A  -> Availability
    C  -> Consistency

    E  -> Else
    L  -> Latency
    C  -> Consistency

    PACELC

    During Partition:
        Availability vs Consistency

    Else:
        Latency vs Consistency

---

# Interview One-Liner

"PACELC extends CAP by stating that if a network partition occurs, a distributed system must choose between Availability and Consistency; otherwise, when the system is healthy, it must choose between Latency and Consistency."
