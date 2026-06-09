# CAP Theorem (Consistency, Availability, Partition Tolerance)

CAP Theorem states that:

> A distributed system cannot simultaneously guarantee **Consistency (C)**, **Availability (A)**, and **Partition Tolerance (P)**.

When a network partition occurs, a distributed system must choose between:

- **CP (Consistency + Partition Tolerance)**
- **AP (Availability + Partition Tolerance)**

It cannot guarantee all three properties at the same time.

---

## Why CAP Theorem Exists

Distributed systems store data across multiple nodes:

```
Node A (New York)
Node B (London)
Node C (Tokyo)
```

If communication between nodes fails, a **network partition** occurs.

During the partition, the system must decide:

1. Maintain consistent data across nodes.
2. Continue serving all requests.

It cannot fully guarantee both.

---

# 1. Consistency (C)

Every read returns the most recent write (or an error).

### Example

```
Balance = $1000
```

After updating the balance:

```
Node A → $1000
Node B → $1000
Node C → $1000
```

All nodes return the same latest value.

### Benefits

- No stale data
- Strong correctness guarantees

### Drawbacks

- Some requests may be rejected during failures

---

# 2. Availability (A)

Every request receives a response.

The response may not contain the latest data.

### Example

```
Node A → 50
Node B → 45
```

A user reading from Node B gets:

```
45
```

Even if the latest value is 50.

### Benefits

- System always responds
- Better uptime

### Drawbacks

- Data can be stale

---

# 3. Partition Tolerance (P)

The system continues operating despite network failures between nodes.

### Example

```
Node A -----X----- Node B
```

Communication fails, but the system continues running.

### Why P Is Important

Partitions can happen because of:

- Network failures
- Router crashes
- Data center outages
- Cloud region failures

In distributed systems, partition tolerance is usually mandatory.

---

# CAP Trade-Off

```
       CAP
      / | \
     /  |  \
    C   A   P
```

During a partition:

```
Choose:
(C + P)

OR

(A + P)
```

Not:

```
(C + A + P)
```

---

# CP Systems (Consistency + Partition Tolerance)

When a partition occurs:

- Consistency is preserved.
- Some requests may be denied.

### Example

```
Client → Node A
Client → Node B
```

Partition occurs.

To avoid inconsistent data:

```
Node B stops accepting writes
```

### Result

✅ Consistent data

❌ Reduced availability

### Examples

- HBase
- ZooKeeper
- MongoDB (strong consistency configurations)

---

# AP Systems (Availability + Partition Tolerance)

When a partition occurs:

- All nodes continue serving requests.
- Data may temporarily differ.

### Example

```
Node A = 100
Node B = 90
```

Both nodes continue responding.

### Result

✅ High availability

❌ Temporary inconsistency

### Examples

- Apache Cassandra
- Riak
- Amazon Dynamo

---

# Why Not CA?

Many people assume a distributed system can simply choose:

```
Consistency + Availability
```

However, in real distributed systems:

```
Partitions are unavoidable
```

Therefore, partition tolerance cannot be ignored.

CA systems generally exist only in:

- Single-server databases
- Shared-memory systems
- Environments where network partitions are not considered

---

# Banking Example

Initial balance:

```
$1000
```

User withdraws:

```
$500
```

A partition occurs.

## CP Approach

The system blocks the transaction until consistency is guaranteed.

```
Transaction rejected or delayed
```

Result:

- No double spending
- Strong consistency

## AP Approach

The transaction is processed on available nodes.

```
Node A → $500
Node B → $1000
```

Result:

- Service remains available
- Temporary inconsistency

Banking systems usually prefer **CP**.

---

# Eventual Consistency

Many AP systems use **eventual consistency**.

Immediately after a write:

```
Node A = 100
Node B = 90
Node C = 95
```

After synchronization:

```
Node A = 100
Node B = 100
Node C = 100
```

All replicas eventually converge.

### Common Use Cases

- Social media likes
- News feeds
- DNS
- Shopping carts

---

# CAP Summary Table

| Property            | Meaning                        | Advantage       | Disadvantage                           |
| ------------------- | ------------------------------ | --------------- | -------------------------------------- |
| Consistency         | Same data everywhere           | Correct results | Lower availability                     |
| Availability        | Every request gets a response  | Better uptime   | Stale data possible                    |
| Partition Tolerance | Works despite network failures | Fault tolerance | Must sacrifice C or A during partition |

---

# Decision Matrix

| System            | Typical Choice          |
| ----------------- | ----------------------- |
| Banking           | CP                      |
| Inventory Systems | CP                      |
| Social Media      | AP                      |
| DNS               | AP                      |
| Messaging Systems | Depends on requirements |

---

# Interview Answer

**Can a distributed system provide C, A, and P simultaneously?**

**Answer:**

No. CAP Theorem states that when a network partition occurs, a distributed system must choose between:

- Consistency + Partition Tolerance (CP)
- Availability + Partition Tolerance (AP)

It cannot guarantee Consistency, Availability, and Partition Tolerance simultaneously.

---

# Key Takeaway

CAP Theorem is fundamentally about **trade-offs during network partitions**.

- **CP systems** prioritize correctness and may reject requests.
- **AP systems** prioritize availability and may return stale data.
- The trade-off becomes relevant when partitions occur, not necessarily during normal operation.
