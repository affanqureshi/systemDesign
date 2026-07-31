
# Retry Mechanisms, Backoff, Idempotency 



# 1. Application-Level Resilience

## Definition
Application-Level Resilience is the ability of an application to automatically recover from temporary failures without affecting the user experience.

<img width="859" height="592" alt="image" src="https://github.com/user-attachments/assets/844cb25b-9351-4032-ba31-808db17b285b" />


## Goal
- High Availability
- Reliability
- Fault Tolerance

## Core Techniques
- Retry
- Backoff
- Jitter
- Idempotency
- Checkpointing

---

# 2. Retry

## Definition
Retry means sending a failed request again.

## When to Use
- Network timeout
- HTTP 503 Service Unavailable
- Temporary server overload
- Database leader election

## Real-World Example
An ATM transaction times out due to a network issue. The ATM automatically retries the request instead of asking the customer to start over.

> **Use retries only for temporary (transient) failures.

---

# 3. Retry Storm (Problem)

When all clients retry at the same time:

```text
10,000 Clients
      │
      ▼
Server Overloaded
      │
      ▼
All Clients Retry
      │
      ▼
Server Crashes
```

## Result
- Retry Storm
- Cascading Failure

❌ Excessive retries can make the outage worse. 

---

# 4. Exponential Backoff

## Definition
Increase the delay after each retry attempt.

```text
1st Retry → 1 second
2nd Retry → 2 seconds
3rd Retry → 4 seconds
4th Retry → 8 seconds
5th Retry → 16 seconds
```

## Benefits
- Gives the server time to recover.
- Reduces unnecessary traffic.

> **Best for overloaded or temporarily unavailable services.** 

---

# 5. Jitter

## Definition
Add a random delay to each retry.

### Without Jitter

```text
All Clients
      │
Retry at 4 seconds
      │
Traffic Spike
```

### With Jitter

```text
Client A → 3.2s
Client B → 4.5s
Client C → 5.1s
Client D → 3.8s
```

## Benefits
- Prevents the **Thundering Herd** problem.
- Distributes traffic evenly.
- Improves system recovery.

> **Best Practice = Exponential Backoff + Jitter** 

---

# 6. Retry Strategy Comparison

| Strategy | Advantages | Disadvantages |
|----------|------------|---------------|
| Naive Retry | Simple | Can cause Retry Storms |
| Exponential Backoff | Gives the server time to recover | Clients may still retry together |
| Exponential Backoff + Jitter | ⭐ Best Practice | Slightly more complex |



---

# 7. Idempotency

## Definition
An operation is **idempotent** if executing the same request multiple times produces the same result.

## Example

```text
POST /payment

1st Request ✅ Payment Processed

Response Lost

Client Retries

Server:
Already Processed
Return Previous Response
```

## Benefits
- Prevents duplicate payments
- Prevents duplicate orders
- Prevents duplicate user creation

# Idempotency Key in Send Money API (Spring Boot) 💳

## What is Idempotency?

**Idempotency** ensures that **the same request can be executed multiple times but the result happens only once**.

### Example

A user sends **Rs. 5,000**.

- ✅ First request → Money transferred
- ❌ Network timeout
- 🔄 User retries

Without Idempotency:
- Rs. 5,000 deducted twice ❌

With Idempotency:
- Server returns the previous response.
- No second debit. ✅

---

# High-Level Architecture

```text
                    +------------------+
                    | Mobile App/Web   |
                    +------------------+
                             |
                             | Idempotency-Key (UUID)
                             |
                             v
                  +----------------------+
                  |     API Gateway      |
                  +----------------------+
                             |
                             v
                +-------------------------+
                | Transfer Controller     |
                +-------------------------+
                             |
                             v
                +-------------------------+
                | Transfer Service        |
                +-------------------------+
                     |              |
          Check Redis/DB      Business Logic
                     |              |
             +-------+-------+      |
             |               |      |
         Key Exists?      Not Found |
             |               |      |
             |          Save PROCESSING
             |               |
             |         Debit Sender
             |               |
             |         Credit Receiver
             |               |
             |        Save SUCCESS
             |               |
             +-------<-------+
                     |
                     v
              Return Response
```

---

# API Flow

```text
Client
   |
   | POST /api/v1/transfers
   | Idempotency-Key: UUID
   |
   v
API
   |
   | Check Redis/DB
   |
   +---------------------------+
   |                           |
Key Found                 Key Not Found
   |                           |
Return Old Response      Save PROCESSING
                               |
                        Debit Sender
                               |
                        Credit Receiver
                               |
                      Save SUCCESS Response
                               |
                         Return Response
```

---

# Sequence Diagram

```text
Client             API            Redis/DB         Account DB

  |                 |                 |                |
  | POST            |                 |                |
  |---------------->|                 |                |
  |                 | Check Key       |                |
  |                 |---------------->|                |
  |                 |                 |                |
  |                 |<----------------| Not Found      |
  |                 |                 |                |
  |                 | Save PROCESSING |                |
  |                 |---------------->|                |
  |                 |                 |                |
  |                 | Debit Sender    |--------------->|
  |                 | Credit Receiver |--------------->|
  |                 |                 |                |
  |                 | Save SUCCESS    |                |
  |                 |---------------->|                |
  |<----------------| Transaction OK  |                |
```

---

# Retry Request

```text
Client
   |
   | Same Idempotency-Key
   |
   v
API
   |
Check Redis/DB
   |
Key Exists
   |
Return Previous Response
   |
No Debit
No Credit
```

---

# Request

```http
POST /api/v1/transfers

Headers

Authorization: Bearer <JWT>

Idempotency-Key: 2fae3d9d-6d2b-4d48-9d83-7a7d12345678

Content-Type: application/json
```

Body

```json
{
  "senderAccount": "10001",
  "receiverAccount": "20002",
  "amount": 5000,
  "currency": "PKR"
}
```

---

# Response (First Request)

```json
{
  "transactionId": "TXN100001",
  "status": "SUCCESS",
  "message": "Money transferred successfully"
}
```

---

# Response (Retry)

```json
{
  "transactionId": "TXN100001",
  "status": "SUCCESS",
  "message": "Money transferred successfully"
}
```

**Same response is returned.**

No new transaction is created.

---

# Database Design

## idempotency_record

| Column | Description |
|----------|------------|
| id | Primary Key |
| idempotency_key | UUID from client |
| request_hash | SHA-256 of request |
| status | PROCESSING / SUCCESS / FAILED |
| response | JSON Response |
| transaction_id | Payment Transaction |
| created_at | Created Time |
| expires_at | TTL |

---

## transaction

| Column | Description |
|----------|------------|
| transaction_id | Payment ID |
| sender_account | Sender |
| receiver_account | Receiver |
| amount | Amount |
| status | SUCCESS / FAILED |
| created_at | Timestamp |

---

# End-to-End Flow

```text
Generate UUID
      |
      v
POST /transfer
      |
      v
API Gateway
      |
      v
Transfer Service
      |
      v
Check Redis/Database
      |
+-----+------+
|            |
Exists      No
|            |
|      Save PROCESSING
|            |
|      Validate Balance
|            |
|      Debit Sender
|            |
|      Credit Receiver
|            |
|      Save Transaction
|            |
|      Save SUCCESS
|            |
+------------+
      |
      v
Return Response
```

---

# Real-World Tech Stack

| Layer | Technology |
|---------|------------|
| Client | Android / iOS / Web |
| API Gateway | Kong / NGINX / AWS API Gateway |
| Backend | Spring Boot |
| Cache | Redis |
| Database | PostgreSQL / MySQL / Oracle |
| Messaging | Kafka / RabbitMQ |
| Monitoring | Prometheus + Grafana |
| Logging | ELK Stack |

---

# Why Redis?

- Extremely fast lookup (milliseconds)
- Prevents duplicate processing
- Reduces database load

---

# Why Store Request Hash?

Example:

Request 1

```text
Key = ABC123
Amount = 5000
```

Request 2

```text
Key = ABC123
Amount = 10000
```

❌ Same key, different request.

Return:

```http
409 Conflict
```

---

# Interview Answer (30 Seconds)

> **The client generates a UUID and sends it in the `Idempotency-Key` header. The server first checks Redis or the database. If the key already exists, it returns the previously stored response without executing the transfer again. If the key is new, the server stores a `PROCESSING` record, performs the debit and credit within a transaction, saves the response with `SUCCESS`, and returns it. This prevents duplicate money transfers caused by retries, network failures, or timeouts.**

---

# 80/20 Interview Points

- ✅ Prevent duplicate payments
- ✅ Client generates UUID
- ✅ Unique `Idempotency-Key`
- ✅ Check Redis/Database first
- ✅ Store `PROCESSING`, `SUCCESS`, `FAILED`
- ✅ Save original response
- ✅ Return stored response on retry
- ✅ Compare request hash
- ✅ Return **409 Conflict** if same key has different payload
- ✅ Use Redis + Database in production fintech systems

---

# 8. Idempotency Key

The client sends a unique request identifier.

```http
POST /payment

Idempotency-Key: abc-123
```

If the same key is received again:

- Do not process the request again.
- Return the previously stored response.

## Commonly Used By
- Stripe
- Visa
- MasterCard

---

# 9. Checkpointing

## Definition
Checkpointing saves the progress of a long-running task so it can continue after a failure.

```text
10,000 Records ✅
20,000 Records ✅
30,000 Records ✅

Server Crash ❌

Restart

Continue from 30,000 ✅
```

## Benefits
- Saves processing time.
- Avoids restarting from the beginning.

## Common Technologies
- Apache Spark
- Apache Flink
- Kafka Streams


---

# 10. Observability

Monitor the following metrics:

- Retry Count
- Backoff Delay
- Error Rate
- Checkpoint Failures

> A sudden increase in retries usually indicates a downstream service issue. :contentReference[oaicite:9]{index=9}

---

# Architecture Flow

```text
Client
   │
   ▼
Send Request
   │
   ▼
Success?
   │
 ┌───────────────┐
 │               │
Yes              No
 │               │
Done          Retry
                 │
                 ▼
      Exponential Backoff
                 │
                 ▼
            Add Jitter
                 │
                 ▼
          Retry Request
                 │
                 ▼
     Same Idempotency Key?
          │            │
         Yes          No
          │            │
Return Cached     Process Request
   Response
```

---

# Interview Questions

### Q1. Why do we use retries?
To recover from temporary failures such as network timeouts, service overload, or transient errors.

### Q2. What is a Retry Storm?
A situation where many clients retry simultaneously, causing additional load and potentially leading to cascading failures.

### Q3. Why use Exponential Backoff?
To gradually reduce retry frequency and give the server time to recover.

### Q4. Why use Jitter?
To randomize retry timing and prevent synchronized retries (Thundering Herd).

### Q5. What is Idempotency?
Executing the same request multiple times produces the same outcome.

### Q6. What is an Idempotency Key?
A unique request identifier used to detect and ignore duplicate requests.

### Q7. Why is Checkpointing important?
It allows long-running tasks to resume from the last saved state instead of starting over.

---

# 80/20 Revision

- ✅ **Retry** → Resend a failed request.
- ✅ **Retry Storm** → Too many simultaneous retries overload the system.
- ✅ **Backoff** → Increase the delay between retries.
- ✅ **Jitter** → Add randomness to retry delays.
- ✅ **Idempotency** → Same request = Same result.
- ✅ **Idempotency Key** → Prevent duplicate processing.
- ✅ **Checkpointing** → Resume from the last saved state.
- ✅ **Best Practice** → **Retry + Exponential Backoff + Jitter + Idempotency + Checkpointing**.
