# Latency

## Definition

**Latency** refers to the amount of time it takes for data to travel from a source to a destination and for the response to return.

### Simple Definition

If you click a website link and the page starts loading after 100 milliseconds (ms), that 100 ms is part of the latency.

---

# Latency Explained in Depth

Latency is usually measured in:

- **Milliseconds (ms)** = 1/1000 of a second
- **Microseconds (μs)** = 1/1,000,000 of a second

## Components of Network Latency

Total latency consists of four major delays:

### 1. Propagation Delay

- Time required for signals to travel through the communication medium.
- Depends primarily on distance.

```
You send a message from your computer ==> The message travels through cables, fiber optics, or the air ==> The message reaches the destination.

The time spent traveling is called Propagation Delay.

```

### 2. Transmission Delay

- Time needed to place all packet bits onto the network.
- Depends on packet size and available bandwidth.

```
Before data can travel, it must first be sent bit by bit onto the cable or wireless connection. The time taken to send all those bits is called Transmission Delay.
```

### 3. Processing Delay

- Time spent by routers, switches, and servers examining and processing packets.

### 4. Queuing Delay

- Time packets spend waiting in buffers due to network congestion.

  This happens when too much data arrives at the same time, and the network device (like a router or switch) cannot send everything immediately.

---

## Total Latency Formula

\[
\text{Latency} = \text{Propagation Delay} + \text{Transmission Delay} + \text{Processing Delay} + \text{Queuing Delay}
\]

---

## Databases

Database latency is the time between sending a query and receiving the result.

---

# One-Way Latency vs Round-Trip Time (RTT)

- **One-Way Latency:** Time taken for data to travel in one direction.
- **Round-Trip Time (RTT):** Time taken for a request and its response to complete.

Most network diagnostic tools (such as `ping`) measure RTT.

---

# Factors Affecting Latency

1. Physical distance between source and destination
2. Network congestion
3. Router and switch processing speed
4. Packet size
5. Transmission medium (fiber, copper, wireless, satellite)
6. Server response time

---

# Reducing Latency

- Use Content Delivery Networks (CDNs)
- Optimize routing paths
- Upgrade network hardware
- Reduce congestion
- Cache frequently accessed data
- Deploy servers closer to users
