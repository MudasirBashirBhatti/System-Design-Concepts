# Consensus Algorithms Explained Like You're a Beginner

## What Problem Do Consensus Algorithms Solve?

Imagine three friends are planning a trip:

```text
Alice
Bob
Charlie
```

They need to decide:

```text
Go to Murree?
Go to Naran?
Go to Skardu?
```

The problem is:

- Alice says Murree
- Bob says Naran
- Charlie didn't receive the message because his internet is down

If everyone acts independently, chaos happens.

To avoid this, they need a way to agree on **one decision**.

This is called **consensus**.

---

# What Is Consensus?

Consensus means:

> Multiple computers agree on the same value even when some computers fail or networks become unreliable.

In distributed systems, consensus ensures all nodes eventually agree on:

- Which data is correct
- Which transaction happened first
- Who is the leader
- What the current system state is

---

# Real-Life Analogy: Group of Friends Choosing a Restaurant

Suppose five friends are choosing a restaurant.

```text
Friend A
Friend B
Friend C
Friend D
Friend E
```

The rule:

> The group must agree on one restaurant.

Voting happens:

```text
Restaurant X -> 4 votes
Restaurant Y -> 1 vote
```

Decision:

```text
Restaurant X
```

Everyone follows the majority.

Consensus algorithms work similarly.

---

# Why Is Consensus Needed?

Consider a distributed database:

```text
Node A
Node B
Node C
```

A user updates their account balance.

Without consensus:

```text
Node A = $1000
Node B = $1000
Node C = $800
```

Now the system doesn't know which value is correct.

Consensus ensures all nodes eventually agree:

```text
Node A = $1000
Node B = $1000
Node C = $1000
```

---

# Leader Analogy

Imagine a classroom.

Students:

```text
Student A
Student B
Student C
Student D
```

Teacher:

```text
Leader
```

The teacher decides:

```text
Today's assignment
```

Students follow.

If the teacher leaves:

```text
Need a new teacher
```

Students vote and elect a new teacher.

Distributed systems do the same thing.

Consensus algorithms often elect a **leader node**.

---

# Key Goals of Consensus

## 1. Agreement

All healthy nodes agree on the same value.

```text
Node A -> YES
Node B -> YES
Node C -> YES
```

---

## 2. Consistency

No two nodes decide differently.

Bad:

```text
Node A -> Transaction Approved
Node B -> Transaction Rejected
```

Good:

```text
All nodes -> Transaction Approved
```

---

## 3. Fault Tolerance

The system continues working even if some nodes fail.

```text
Node A -> Alive
Node B -> Alive
Node C -> Crashed
```

Cluster still works.

---

# Example: Distributed Database

Suppose:

```text
Node A
Node B
Node C
```

User writes:

```text
Balance = $1000
```

Leader receives request:

```text
Node A
```

Leader sends update:

```text
Node B
Node C
```

When majority acknowledge:

```text
2 of 3 nodes
```

Write succeeds.

This is consensus.

---

# Popular Consensus Algorithms

## 1. Raft

Most beginner-friendly consensus algorithm.

Uses:

```text
Leader
Followers
```

### How It Works

Step 1:

```text
Leader receives request
```

Step 2:

```text
Leader replicates to followers
```

Step 3:

```text
Majority confirms
```

Step 4:

```text
Commit change
```

Example:

```text
Node A (Leader)
Node B (Follower)
Node C (Follower)
```

User writes:

```text
Balance = $1000
```

Leader sends update.

If:

```text
Node B = YES
Node C = YES
```

Majority achieved.

Transaction committed.

---

# Leader Failure in Raft

Suppose:

```text
Node A (Leader)
```

crashes.

Followers notice:

```text
Leader missing
```

Election begins.

```text
Node B votes
Node C votes
```

Winner becomes:

```text
New Leader
```

System continues running.

---

# 2. Paxos

Older and more complex consensus algorithm.

Goal:

```text
All nodes agree on one value
```

Even when:

- Messages are delayed
- Nodes crash
- Networks fail

Paxos is powerful but difficult to understand and implement.

Because of this, many modern systems prefer Raft.

---

# 3. Byzantine Fault Tolerance (BFT)

Imagine:

```text
5 generals planning an attack
```

One general is a traitor.

Some generals receive:

```text
Attack
```

Others receive:

```text
Retreat
```

How can honest generals agree?

This is called the:

```text
Byzantine Generals Problem
```

BFT algorithms solve this.

Used heavily in:

- Blockchains
- Cryptocurrencies
- Distributed ledgers

---

# Consensus vs Distributed Transactions

Many beginners confuse these.

## Consensus

Question:

```text
What is the correct state?
```

Example:

```text
Which transaction happened first?
```

Goal:

```text
Agreement
```

---

## Distributed Transaction

Question:

```text
Should multiple operations succeed together?
```

Example:

```text
Reserve inventory
Charge card
Create shipment
```

Goal:

```text
Atomicity
```

---

# Real-World Usage

Consensus algorithms are used in:

### Databases

- :contentReference[oaicite:0]{index=0}
- :contentReference[oaicite:1]{index=1}
- :contentReference[oaicite:2]{index=2}

### Infrastructure

- :contentReference[oaicite:3]{index=3}
- :contentReference[oaicite:4]{index=4}
- :contentReference[oaicite:5]{index=5}

### Blockchains

- :contentReference[oaicite:6]{index=6}
- :contentReference[oaicite:7]{index=7}

---

# Simple Summary

Think of consensus as a group decision-making process.

```text
Multiple computers
        ↓
Need to agree
        ↓
Vote / Coordinate
        ↓
Reach majority
        ↓
Use one shared truth
```

A **consensus algorithm** is a mechanism that allows multiple computers in a distributed system to agree on a single, consistent state—even when some computers crash, messages are delayed, or parts of the network fail.
