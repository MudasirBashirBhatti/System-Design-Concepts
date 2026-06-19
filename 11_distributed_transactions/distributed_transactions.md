# Distributed Transactions

## What Problem Do Distributed Transactions Solve?

Imagine you're organizing a wedding and need to:

1. Book the venue
2. Book the caterer
3. Book the photographer

You only want the wedding to happen if **all three bookings succeed**.

If the venue and caterer are booked, but the photographer is unavailable, you don't want to pay for the venue and caterer and end up with a half-planned wedding.

**Rule:**

> Either everything succeeds, or everything gets canceled.

This is exactly what a **distributed transaction** does.

---

# Normal Transaction (Single Database)

Suppose you're transferring $100 between two accounts in the same database.

Initial state:

```text
Account A = $500
Account B = $200
```

Steps:

```text
1. Deduct $100 from Account A
2. Add $100 to Account B
```

If step 2 fails:

```text
Rollback step 1
```

Final state:

```text
Account A = $500
Account B = $200
```

Everything stays consistent because one database controls both operations.

---

# Distributed Transaction

Now imagine transferring money between two different banks.

```text
Bank A Database
Bank B Database
```

Steps:

```text
1. Deduct $100 from Bank A
2. Add $100 to Bank B
```

What if:

```text
Step 1 succeeds
Step 2 fails
```

Result:

```text
Bank A lost $100
Bank B never received it
```

Money has effectively disappeared.

Distributed transactions prevent situations like this.

---

# Restaurant Combo Meal Analogy

Suppose you order a combo meal.

The order requires:

```text
Burger Counter
Drink Counter
Fries Counter
```

Each counter operates independently.

The restaurant promises:

> You either get the entire combo or nothing.

### Success Case

```text
Burger ✓
Drink ✓
Fries ✓
```

Order completed.

### Failure Case

```text
Burger ✓
Drink ✓
Fries ✗
```

Restaurant cancels everything:

```text
Burger cancelled
Drink cancelled
Fries cancelled
```

No partial order.

---

# How Is This Coordinated?

A manager coordinates all counters.

```text
          Manager
        /    |    \
       /     |     \
Burger   Drink   Fries
```

Manager asks:

```text
Can you complete this order?
```

Responses:

```text
Burger: YES
Drink: YES
Fries: YES
```

Manager then says:

```text
COMMIT
```

Everyone finalizes.

If anyone says:

```text
NO
```

Manager says:

```text
ROLLBACK
```

Everyone cancels.

This process is called **Two-Phase Commit (2PC)**.

---

# Two-Phase Commit (2PC)

## Phase 1: Prepare

Coordinator asks:

```text
Ready?
```

Services respond:

```text
YES
YES
YES
```

No one commits yet.

---

## Phase 2: Commit

Coordinator sends:

```text
Commit now.
```

All participants save changes permanently.

---

# Real-World Example: E-Commerce

Suppose an online store has:

```text
Order Service
Inventory Service
Payment Service
Shipping Service
```

Customer buys a laptop.

Steps:

```text
1. Create Order
2. Reserve Inventory
3. Charge Credit Card
4. Create Shipment
```

Everything must succeed.

If payment fails:

```text
Cancel Order
Release Inventory
Cancel Shipment
```

No partial purchase remains.

---

# Why Distributed Transactions Are Hard

Imagine:

```text
Order Service -> Success
Inventory Service -> Success
Payment Service -> Success
```

Then the network fails before Shipping Service responds.

Now nobody knows:

```text
Did shipping succeed?
Did shipping fail?
```

This uncertainty creates major challenges.

Distributed systems must deal with:

- Network outages
- Database node failures
- Service crashes
- Timeouts
- Partial failures

---

# Why Many Modern Systems Avoid Them

Traditional distributed transactions can:

- Slow down systems
- Lock resources for a long time
- Reduce scalability
- Become difficult to recover from during failures

Large companies often prefer the **Saga Pattern** instead.

---

# Saga Pattern Analogy

Back to the wedding example:

```text
Book Venue ✓
Book Caterer ✓
Book Photographer ✗
```

Instead of one big rollback mechanism:

```text
Cancel Caterer
Cancel Venue
```

Each service knows how to undo its own work.

These are called **compensating transactions**.

Examples:

```text
Reserve Inventory -> Release Inventory
Charge Credit Card -> Refund Payment
Create Shipment -> Cancel Shipment
```

---

# Distributed Transaction vs Saga

| Distributed Transaction (2PC)   | Saga Pattern                       |
| ------------------------------- | ---------------------------------- |
| All services commit together    | Each service commits independently |
| Strong consistency              | Eventual consistency               |
| Coordinator controls everything | Services coordinate through events |
| Can block during failures       | More resilient                     |
| Simpler data consistency        | More complex business logic        |
| Common in traditional systems   | Common in microservices            |

---

# Simple Summary

Think of a distributed transaction as a group project involving:

```text
Alice
Bob
Charlie
```

Rule:

> Either everyone submits their part, or nobody gets credit.

Coordinator asks:

```text
Ready?
```

If everyone says:

```text
YES
```

Then:

```text
Submit Project
```

If even one person says:

```text
NO
```

Then:

```text
Cancel Everything
```

A **distributed transaction** ensures that operations across multiple databases or services either **all succeed together** or **all fail together**, keeping the system consistent.
