# Leader Election

## System Design Explanation

In a distributed system, multiple servers (nodes) work together as a cluster.

```text
Node A
Node B
Node C
```

Many distributed systems need **one node to act as the leader**.

The leader is responsible for tasks such as:

- Accepting write requests
- Coordinating replication
- Managing distributed locks
- Scheduling jobs
- Making cluster-wide decisions

Example:

```text
           Client
              |
              v
        Leader Node
          /      \
         v        v
     Follower  Follower
```

Instead of all nodes accepting writes independently, only the leader accepts writes and propagates them to followers.

---

## The Problem

Suppose:

```text
Node A = Leader
Node B = Follower
Node C = Follower
```

If Node A crashes:

```text
Node A ❌
Node B ?
Node C ?
```

The cluster still needs someone to:

- Process writes
- Coordinate updates
- Manage the cluster

Therefore, the remaining nodes must choose a new leader.

This process is called **Leader Election**.

---

## How Leader Election Works

### Step 1: Leader Failure Detection

Followers continuously receive heartbeat messages from the leader.

```text
Leader -> I'm alive
Leader -> I'm alive
Leader -> I'm alive
```

If heartbeats stop for a certain period:

```text
Leader not responding
```

the followers assume the leader has failed.

---

### Step 2: Election Starts

One of the followers becomes a candidate.

```text
Node B -> Candidate
```

It asks other nodes for votes.

```text
Vote for me?
```

---

### Step 3: Majority Voting

Example:

```text
Node B -> Vote for me?
Node C -> YES
```

Votes:

```text
Node B = 2 votes
```

Since a majority is achieved:

```text
Node B becomes Leader
```

---

### Step 4: New Leader Takes Control

```text
Node B = Leader
Node C = Follower
```

Node B starts sending heartbeats and handling requests.

The cluster continues operating without manual intervention.

---

## Why Leader Election Is Important

Without leader election:

```text
Node B thinks it's leader
Node C thinks it's leader
```

Both may accept writes.

```text
Node B -> Balance = $1000
Node C -> Balance = $1200
```

Now data becomes inconsistent.

Leader election ensures:

- Only one leader exists
- Data remains consistent
- The cluster continues functioning after failures

---

# Analogy (One Simple Analogy)

Imagine a company team with one manager.

```text
Manager
Employee A
Employee B
Employee C
```

The manager:

- Assigns tasks
- Approves decisions
- Coordinates the team

One day the manager resigns.

```text
Manager ❌
```

The team cannot stop working.

So employees hold a quick vote:

```text
Employee A -> 2 votes
Employee B -> 1 vote
```

Employee A becomes the new manager and continues coordinating the team.

This is exactly what **leader election** does in distributed systems:

> When the current leader fails, the remaining nodes vote and automatically select a new leader so the system can continue operating.
