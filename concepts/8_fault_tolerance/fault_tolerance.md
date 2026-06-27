# Fault Tolerance (System Design)

## Definition

Fault Tolerance is the ability of a system to continue operating even when some components fail.

Examples:

- Server crash
- Database node failure
- Network outage
- Service failure

The goal is to minimize downtime and keep the system available.

---

## 1. Remove Single Point of Failure (SPOF)

Bad Design:

    Users
      |
    Server
      |
      DB

If the database fails, the entire system goes down.

Better Design:

        Users
          |
    Load Balancer
       /      \

Server1 Server2
\ /
DB Cluster

---

## 2. Redundancy

Keep extra resources available.

    Server A
    Server B
    Server C

If Server A fails:

    A ❌
    B ✅
    C ✅

The service continues running.

---

## 3. Replication

Store data on multiple nodes.

          Primary
         /       \
    Replica1   Replica2

If the Primary fails:

       Replica1
    (New Primary)

---

## 4. Failover

Automatically switch to a backup component.

Normal:

    Primary → Active
    Backup  → Standby

Failure:

    Primary ❌
    Backup  ✅

---

## 5. Health Checks

Continuously monitor component health.

Example:

    GET /health

Response:

    Healthy ✅
    Unhealthy ❌

Unhealthy instances are removed from traffic.

---

## 6. Retries with Exponential Backoff

Handle temporary failures.

Bad:

    Retry
    Retry
    Retry

Good:

    Retry after 1s
    Retry after 2s
    Retry after 4s
    Retry after 8s

This prevents overwhelming the failing service.

---

## 7. Circuit Breaker

Prevents cascading failures.

Without Circuit Breaker:

    Service A ---> Service B ❌

Service A keeps sending requests and makes the situation worse.

With Circuit Breaker:

    Service A -X-> Service B

Requests are temporarily blocked until recovery.

---

## 8. Graceful Degradation

Provide reduced functionality instead of complete failure.

Normal:

    Product
    Reviews
    Recommendations
    Cart

Recommendation Service Down:

    Product
    Reviews
    Cart

Users can still complete purchases.

---

## 9. Backups

Replication is NOT a backup.

Example:

    DELETE users;

The deletion may be replicated to all replicas.

Backups protect against:

- Accidental deletion
- Data corruption
- Disaster recovery

---

## Fault-Tolerant Architecture

                Users
                  |
            Load Balancer
             /        \
        App-1        App-2
           |           |
           +-----------+
                 |
            DB Cluster
           /    |    \
        DB1   DB2   DB3

Features:

- Redundancy
- Replication
- Failover
- Health Checks

---

## Final Answer

"Fault tolerance is the ability of a system to continue functioning despite failures. It is achieved through redundancy, replication, failover, health checks, retries, circuit breakers, backups, and monitoring."

Memory trick:
Fault Tolerance = RRF-HRCB

R → Redundancy
R → Replication
F → Failover
H → Health Checks
R → Retries
C → Circuit Breaker
B → Backups
