# Circuit Breaker Pattern

## Definition

A Circuit Breaker is a fault-tolerance pattern that prevents a system from repeatedly calling a failing service.

It protects resources and prevents cascading failures.

---

## Why Do We Need It?

Example:

    Service A
        |
    Service B

If Service B is down and Service A keeps sending requests:

    Request ❌
    Request ❌
    Request ❌
    Request ❌

Problems:

- Thread exhaustion
- Connection pool exhaustion
- Increased latency
- Cascading failures

---

## Solution

Place a Circuit Breaker between services.

    Service A
        |

Circuit Breaker
|
Service B

The Circuit Breaker monitors failures and decides whether requests should be allowed.

---

# Circuit Breaker States

## 1. Closed State (Normal)

Everything is healthy.

    Service A
        |
    [CLOSED]
        |
    Service B

Requests flow normally.

---

## 2. Open State (Failure Detected)

Too many failures occur.

Example:

    10 Requests
    7 Failures

Circuit opens.

    Service A
        |
     [OPEN]
        X
    Service B

Requests are rejected immediately.

Benefits:

- No waiting for timeouts
- No blocked threads
- Faster responses

---

## 3. Half-Open State (Recovery Check)

After a cooldown period:

    30 Seconds Passed

Circuit allows a few test requests.

    Service A
        |

[HALF-OPEN]
|
Service B

If requests succeed:

    CLOSED ✅

If requests fail:

    OPEN ❌

---

# State Diagram

                     Failures
          +-----------------------------+
          |                             |
          v                             |
      +--------+                   +--------+
      | CLOSED | ----------------> | OPEN   |
      +--------+                   +--------+
           ^                            |
           |                            |
           |    Cooldown Timeout        |
           |                            v
           |                      +-----------+
           +----------------------| HALF-OPEN |
                  Success         +-----------+

---

# Circuit Breaker vs Retry

## Retry

    Request
       |
      Fail
       |
    Try Again

Used for temporary failures.

---

## Circuit Breaker

    Request
       |
    Service Down
       |

Stop Requests

Used for persistent failures.

---

## Together

    Request
       |

Retry 3 Times
|
Still Failing?
|
Circuit Breaker Opens

This is the most common production setup.

---

# Where Is It Used?

## Backend (Most Common)

    Order Service
          |

Circuit Breaker
|
Payment Service

Prevents cascading failures between microservices.

---

## API Gateway

         Client
            |
      API Gateway
            |
      Payment API

If Payment API fails:

    Gateway Opens Circuit
           |
     Immediate Error

Protects backend services.

---

## Frontend (Optional)

    React App
         |

Recommendation API

Repeated failures:

    Request ❌
    Request ❌
    Request ❌

Frontend can:

- Stop requests temporarily
- Show cached data
- Hide unavailable features

Purpose: Better user experience.

---

# Benefits

- Prevents cascading failures
- Protects threads and connections
- Reduces latency
- Improves availability
- Enables graceful degradation
- Faster failure response

---

# Quick Definition

"A Circuit Breaker is a fault-tolerance pattern that prevents repeated calls to a failing service. It has three states: Closed, Open, and Half-Open. When failures exceed a threshold, the circuit opens and blocks requests. After a cooldown period, test requests are allowed. If they succeed, the circuit closes; otherwise, it reopens. This prevents cascading failures and improves system reliability. use opossum tool in node apps"
