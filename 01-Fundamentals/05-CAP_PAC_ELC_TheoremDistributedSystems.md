# CAP vs. PACELC Theorem (80/20 Interview Notes)

> **Goal:** Understand the trade-offs in distributed systems.

---

# CAP Theorem

**CAP = Consistency + Availability + Partition Tolerance**

**Definition**

When a **Network Partition (P)** occurs, a distributed system can choose **either Consistency (C) or Availability (A)**, but **not both**.

<img width="758" height="525" alt="image" src="https://github.com/user-attachments/assets/dac86135-278c-422a-bb24-dd76e385d5dd" />

> **CAP only applies when there is a network partition.**
<img width="693" height="456" alt="image" src="https://github.com/user-attachments/assets/4eb3cfc6-80d4-4660-a6ba-87f3d09cd5c4" />

---

## Components

### 🟢 Consistency (C)

Every client sees the **latest data**.

**Example**

```text
Balance = Rs.1000

User withdraws Rs.200

All servers immediately show Rs.800
```

✅ Latest data everywhere

---

### 🟢 Availability (A)

Every request gets a response.

The response may contain **stale (old) data**.

```text
Replica hasn't synced yet

User still gets a response
```

✅ Service never goes down

---

### 🟢 Partition Tolerance (P)

Servers cannot communicate because of a network failure.

```text
Server A  ❌------❌  Server B
```

Distributed systems must tolerate network failures.

---

## CAP Decision

```text
Network Partition?

        YES
         |
   ----------------
   |              |
Consistency   Availability
     (CP)         (AP)
```

---

## Real-World Examples

### 🏦 Banking System (CP)

```text
Network Failure

↓

Transaction waits or fails

↓

Correct balance maintained
```

✔ Data correctness is more important than availability.

---

### 📱 Social Media (AP)

```text
Server A = 100 Likes

Server B = 98 Likes
```

Users continue using the app.

Replicas sync later.

✔ Availability is more important.

---

# Limitation of CAP

CAP only explains **what happens during a network partition**.

It does **not** explain what happens during normal operation.

This is why **PACELC** was introduced.

---

# PACELC Theorem

PACELC extends CAP.
<img width="723" height="428" alt="image" src="https://github.com/user-attachments/assets/69f74791-14d7-4fd0-84b8-e1f85ddd2c45" />

**Rule**

```text
If Partition

Choose

Consistency
OR
Availability

Else

Choose

Latency
OR
Consistency
```

---

## Meaning

### If Partition Exists

Choose

- Consistency
- Availability

---

### If No Partition

Choose

- Low Latency
- Strong Consistency

---

## PACELC Decision

```text
             Partition?

             Yes
              |
      Consistency
             OR
        Availability

             No
              |
          Latency
             OR
        Consistency
```

---

# Latency vs Consistency

### Low Latency

Return response immediately.

Replicas sync later.

```text
Fast

↓

Eventually Consistent
```

---

### Strong Consistency

Wait until replicas acknowledge the write.

```text
Slightly Slower

↓

Latest Data Guaranteed
```

---

# Database Examples

| Database | CAP | PACELC | Reason |
|----------|-----|---------|--------|
| Cassandra | AP | PA/EL | High availability, low latency |
| DynamoDB | AP | PA/EL | Fast response, eventual consistency |
| MongoDB (Majority Writes) | CP | PC/EC | Strong consistency |
| ZooKeeper | CP | PC/EC | Coordination service |
| etcd | CP | PC/EC | Kubernetes state store |
| HBase | CP | PC/EC | Strong consistency |

---

# CAP vs PACELC

| Feature | CAP | PACELC |
|---------|------|---------|
| Focus | Network Partition | Partition + Normal Operation |
| Failure | Consistency vs Availability | Consistency vs Availability |
| Normal Operation | ❌ Not Covered | ✅ Latency vs Consistency |
| Practical | Limited | More Realistic |

---

# Real-World Mapping

## Banking

```text
Money Transfer

↓

Correct Balance

↓

CP

↓

PC/EC
```

Priority:

- Data Correctness
- No Double Spending

---

## WhatsApp

```text
Message Sent ✓

↓

Delivered ✓✓

↓

Eventually Synced
```

Priority:

- Fast Response
- High Availability

---

## Kubernetes

```text
etcd

↓

Strong Consistency

↓

Cluster State Always Correct
```

---

# Interview Questions

## Q1. What is CAP Theorem?

**Answer**

CAP theorem states that when a **network partition** occurs, a distributed system can provide either **Consistency** or **Availability**, but not both simultaneously.

---

## Q2. Why was PACELC introduced?

**Answer**

CAP only explains behaviour during a network partition. PACELC extends CAP by also explaining the trade-off between **Latency** and **Consistency** during normal operation.

---

## Q3. CAP vs PACELC?

| CAP | PACELC |
|------|---------|
| Works only during partition | Works during partition and normal operation |
| C vs A | C vs A, Else L vs C |
| Simpler | More practical |

---

# Memory Trick

```text
CAP

Partition?
      |
Consistency
      OR
Availability
```

```text
PACELC

Partition?
      |
Consistency
      OR
Availability

Else
      |
Latency
      OR
Consistency
```

---

# 80/20 Interview Takeaways

- CAP = **Network Failure → Consistency vs Availability**
- PACELC = **Network Failure → C vs A**
- PACELC = **Normal Operation → Latency vs Consistency**
- Banking → **CP / PC-EC**
- ZooKeeper & etcd → **CP / PC-EC**
- Cassandra & DynamoDB → **AP / PA-EL**
- Social Media → **Availability + Low Latency**
- Financial Systems → **Consistency First**
