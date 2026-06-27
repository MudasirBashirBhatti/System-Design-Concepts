# Cache Eviction Strategies

The rules used to decide **which data should be removed from cache when it becomes full**.

---

## Why it is needed

- Cache memory is limited
- New data keeps coming in
- Old data must be removed efficiently
- Goal: keep frequently used data and remove less useful data

---

## Common Cache Eviction Strategies

### 1. LRU (Least Recently Used)

Removes the data that was **not used for the longest time**.

- Idea: If it wasn’t used recently, it is less likely to be used again.

---

### 2. FIFO (First In First Out)

Removes the data that **entered the cache first**.

- Oldest data is removed first
- Simple but not always optimal

---

### 3. LFU (Least Frequently Used)

Removes data that is **used the least number of times**.

- Tracks how often data is accessed
- Keeps frequently used data longer

---

### 4. Random Replacement

Removes a **random cache entry**.

- Very simple
- Used when speed is more important than optimization

---

### 5. MRU (Most Recently Used)

Removes the data that was **used most recently**.

- Useful in some special access patterns

---

## Quick Comparison

| Strategy | Removes               |
| -------- | --------------------- |
| LRU      | Least recently used   |
| FIFO     | Oldest inserted       |
| LFU      | Least frequently used |
| Random   | Random item           |
| MRU      | Most recently used    |

---
