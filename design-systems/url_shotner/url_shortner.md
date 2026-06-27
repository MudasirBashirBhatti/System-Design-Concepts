# Design TinyURL / Bitly (URL Shortener) — Complete System Design

# 1. Problem Statement

Design a URL shortening service similar to TinyURL or Bitly.

Example:

```
https://tiny.ly/abc123
```

Redirects to:

```
https://www.example.com/very/long/url/path
```

The system should be highly available, horizontally scalable, globally distributed, and capable of handling billions of URLs and billions of redirects per day.

---

# 2. Functional Requirements

### Core Features

1. Create Short URL
2. Redirect Short URL → Long URL
3. Custom Alias Support
4. URL Expiration
5. User Accounts
6. URL Management
7. Analytics Dashboard

### Analytics

* Total Clicks
* Click Timeline
* Country Distribution
* Device Type
* Browser
* Referrer

---

# 3. Non-Functional Requirements

### Availability

* 99.99% uptime

### Scalability

Support:

* 1 Billion URLs
* 10 Billion Redirects/Day
* 100 Million URL Creations/Day

### Low Latency

Redirect latency:

* p50 < 10ms
* p99 < 100ms

### Durability

URLs must never be lost.

### Security

* Spam prevention
* Malware detection
* Rate limiting

---

# 4. Capacity Estimation

## Redirect Traffic

Assume:

```
10 Billion Redirects / Day
```

RPS:

```
10,000,000,000 / 86,400

≈ 115,740 RPS
```

Peak:

```
~500,000 RPS
```

---

## URL Creation Traffic

```
100 Million URLs / Day
```

RPS:

```
100,000,000 / 86,400

≈ 1,157 Writes/Sec
```

Peak:

```
~5,000 Writes/Sec
```

---

## Storage Estimation

Average URL:

```
Long URL: 500 Bytes
Metadata: 100 Bytes
```

Total:

```
600 Bytes
```

For 1 Billion URLs:

```
600 GB
```

Replication Factor = 3

```
1.8 TB
```

---

# 5. High-Level Architecture

```text
                +-------------+
                | LoadBalancer|
                +------+------+
                       |
       +---------------+---------------+
       |                               |
+------v------+               +--------v-------+
| URL Service |               | Redirect API   |
+------+------+               +--------+-------+
       |                               |
       |                               |
       +------------+------------------+
                    |
             +------v------+
             | Redis Cache |
             +------+------+
                    |
             +------v------+
             | URL Storage |
             +------+------+
                    |
             +------v------+
             | Kafka       |
             +------+------+
                    |
             +------v------+
             | Analytics   |
             +-------------+
```

---

# 6. API Design

## Create URL

### Request

```http
POST /api/v1/shorten
```

```json
{
  "url":"https://example.com/page"
}
```

### Response

```json
{
  "shortUrl":"https://tiny.ly/aB12Cd"
}
```

---

## Redirect

```http
GET /aB12Cd
```

Response:

```http
301 Redirect
Location: https://example.com/page
```

---

## Analytics

```http
GET /api/v1/analytics/aB12Cd
```

---

# 7. Database Schema

## URLs Table

```sql
CREATE TABLE urls(
    id BIGINT PRIMARY KEY,
    short_code VARCHAR(10) UNIQUE,
    long_url TEXT,
    user_id BIGINT,
    created_at TIMESTAMP,
    expires_at TIMESTAMP,
    status VARCHAR(20)
);
```

Indexes:

```sql
INDEX(short_code)
INDEX(user_id)
```

---

# 8. URL Generation Strategies

## Option 1: Hashing

```text
MD5(URL)
```

Take first few characters.

### Problem

* Collisions
* Duplicate handling

Not preferred.

---

## Option 2: Random String

Generate:

```
aZ91Kd
```

Need collision checks.

At large scale collision probability increases.

---

## Option 3: Counter + Base62

Most common production solution.

Example:

```
123456
```

Convert to Base62.

Alphabet:

```
[a-z]
[A-Z]
[0-9]
```

Total:

```
62 Characters
```

---

## Base62 Capacity

```
62^7
=
3.5 Trillion
```

Possible URLs.

---

# 9. Distributed ID Generation

Single Auto Increment becomes bottleneck.

Instead use:

## Snowflake

Structure:

```
41 bits timestamp
10 bits machine id
12 bits sequence
```

Total:

```
64 bits
```

Benefits:

* Globally unique
* Time sortable
* Distributed
* No central coordinator

---

# 10. Redirect Flow

```text
User
 |
 v
CDN
 |
 v
Redirect Service
 |
 v
Redis
 |
 +---- Cache Hit ----> Return URL
 |
 Cache Miss
 |
 v
Database
 |
 v
Update Cache
 |
 v
301 Redirect
```

---

# 11. Caching Strategy

Read traffic dominates.

Most URLs follow Zipf Distribution.

Example:

```
1% URLs
Generate
90% Traffic
```

---

## L1 Cache

Inside application memory.

Examples:

* Caffeine
* Guava

Latency:

```
Microseconds
```

---

## L2 Cache

Redis Cluster.

Key:

```
short:abc123
```

Value:

```
long_url
```

---

## L3 Cache

CDN Edge Cache

Reduces global latency.

---

# 12. Database Selection

## MySQL

Pros:

* Strong consistency
* Simplicity

Cons:

* Scaling limitations

---

## Cassandra

Pros:

* Massive scale
* Horizontal expansion

Cons:

* Eventual consistency

---

## Recommended

Medium Scale:

```
MySQL + Redis
```

Very Large Scale:

```
Cassandra + Redis
```

---

# 13. Sharding

With billions of URLs we need partitioning.

## Hash-Based Sharding

```text
hash(short_code) % N
```

Example:

```
Shard 0
Shard 1
Shard 2
Shard 3
```

---

## Consistent Hashing

Advantages:

* Easier scaling
* Minimal rebalancing

Preferred.

---

# 14. Analytics Pipeline

Never update analytics synchronously.

Bad:

```text
Redirect
 |
DB Write
 |
Return
```

High latency.

---

## Correct Design

```text
Redirect
 |
Kafka Event
 |
Immediate Response
```

---

## Event

```json
{
  "short_code":"abc123",
  "timestamp":"...",
  "country":"US",
  "device":"Mobile"
}
```

---

## Processing

```text
Kafka
 |
Flink
 |
ClickHouse
```

---

# 15. CDN Optimization

Deploy edge locations globally.

```text
US
EU
Asia
Australia
```

Flow:

```text
User
 |
Nearest Edge
 |
Cached Redirect
 |
Origin if Miss
```

Benefits:

* Lower latency
* Reduced origin load

---

# 16. Multi-Region Design

Regions:

```text
US-East
US-West
Europe
Asia
```

---

## Read Traffic

Route users using:

```
GeoDNS
```

to nearest region.

---

## Write Traffic

Active-Active.

Each region:

* Generates Snowflake IDs
* Stores locally
* Replicates globally

---

# 17. Hot URL Problem

Example:

```
tiny.ly/worldcup
```

Gets:

```
2 Million RPS
```

---

## Solution

Layered caching:

```text
CDN
 |
Memory Cache
 |
Redis
 |
Database
```

Database never sees most requests.

---

# 18. URL Expiration

Schema:

```sql
expires_at
```

During redirect:

```text
Lookup URL
 |
Expired?
 |
Yes -> 404
 |
No -> Redirect
```

Cleanup using background jobs.

---

# 19. Custom Aliases

Request:

```json
{
  "alias":"openai"
}
```

Generated URL:

```
tiny.ly/openai
```

Requirement:

```sql
UNIQUE(alias)
```

---

# 20. URL Deduplication

Question:

Should multiple users shortening:

```
https://google.com
```

receive same shortcode?

### Option A

One shortcode per unique URL.

Pros:

* Storage savings

Cons:

* Analytics mixing

---

### Option B

One shortcode per request.

Pros:

* Better analytics

Cons:

* More storage

Bitly generally prefers separate links.

---

# 21. Security

## Malware Detection

Before storing:

```text
URL
 |
Security Scanner
 |
Allow / Block
```

Examples:

* Google Safe Browsing
* Internal threat systems

---

## Rate Limiting

Redis Token Bucket.

Example:

```
100 requests/minute/user
```

---

## Abuse Detection

Prevent:

* Spam
* Phishing
* Bot creation

---

# 22. Bloom Filters

Problem:

Cache misses can overload DB.

Solution:

```text
Bloom Filter
 |
Probably Exists?
 |
No -> Return 404
 |
Yes -> Query Cache/DB
```

Reduces unnecessary DB traffic.

---

# 23. Consistency

## URL Creation

Need:

```
Strong Consistency
```

Reason:

Cannot generate duplicate shortcode.

---

## Analytics

Can be:

```
Eventual Consistency
```

Dashboard lag of few seconds acceptable.

---

# 24. Disaster Recovery

## Replication

Cross-region replication.

```text
US
 |
EU
 |
Asia
```

---

## Recovery Goals

### RPO

```
0
```

No data loss.

### RTO

```
< 5 minutes
```

Fast recovery.

---

# 25. Monitoring

## Application Metrics

* Requests/sec
* Error Rate
* Latency

### Percentiles

* p50
* p95
* p99

---

## Cache Metrics

* Hit Rate
* Miss Rate

---

## Kafka Metrics

* Consumer Lag
* Throughput

---

## Database Metrics

* QPS
* Replication Lag

---

# 26. Failure Scenarios

## Redis Failure

Fallback:

```text
Redis Down
 |
Database Read
 |
Rebuild Cache
```

---

## Region Failure

```text
US-East Down
 |
Traffic
 |
Europe
```

via GeoDNS failover.

---

## Kafka Failure

Persist events locally and retry.

---

# 27. Trade-Off Analysis

## URL Generation

| Approach           | Pros     | Cons             |
| ------------------ | -------- | ---------------- |
| Hash               | Simple   | Collisions       |
| Random             | Easy     | Collision checks |
| Counter            | Compact  | Centralized      |
| Snowflake + Base62 | Scalable | More complex     |

---

## Storage

| MySQL              | Cassandra            |
| ------------------ | -------------------- |
| Strong Consistency | Massive Scale        |
| Easier Operations  | Eventual Consistency |

---

## Analytics

| Sync         | Async    |
| ------------ | -------- |
| Accurate     | Fast     |
| High Latency | Scalable |

---

# 28. Final Production Architecture

```text
                       Users
                         |
                     GeoDNS
                         |
                    Global CDN
                         |
                   Load Balancer
                         |
        +----------------+----------------+
        |                                 |
  URL Creation API                 Redirect API
        |                                 |
        |                           L1 Cache
        |                                 |
        |                           Redis Cluster
        |                                 |
  Snowflake Service                Cassandra
        |                                 |
        +---------------+-----------------+
                        |
                      Kafka
                        |
                 Flink / Spark
                        |
                    ClickHouse
                        |
                 Analytics API
```

---

# 29. Why This Design Works

### Scalability

* Horizontal scaling
* Distributed ID generation
* Sharding

### Performance

* CDN
* Memory Cache
* Redis

### Reliability

* Multi-region
* Replication
* Failover

### Analytics

* Kafka
* Stream Processing
* ClickHouse

### Security

* Rate limiting
* Malware scanning
* Abuse prevention

This architecture can comfortably support billions of URLs, hundreds of thousands to millions of redirects per second, and global low-latency access while maintaining high availability and operational resilience.
