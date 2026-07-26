# Consistency Models in Distributed Databases 



# Event Ordering Models

Event Ordering Models define **how events are ordered across multiple servers** in a distributed system.

---

# 1. Total Ordering

## Definition

Every node sees **all events in the exact same order**.

```
A → B → C
```

Every server processes:

```
A

↓

B

↓

C
```

### Real-World Example

**Banking Transaction**

```
Deposit

↓

Withdraw

↓

Balance Check
```

Every server follows the same sequence.

### Pros

- Strong consistency
- Same view for everyone

### Cons

- High latency
- More coordination

### Memory Trick

**Everyone sees the SAME order.**

---

# 2. Partial Ordering

## Definition

Only **related events** are ordered; independent events can execute in any order.

```
Payment

↓

Order Confirmation
```

But

```
Customer A Order

||

Customer B Order
```

No ordering required.

### Real-World Example

**eCommerce**

Customer A and Customer B place orders simultaneously.

Their orders don't need a global order, but each customer's **Payment → Confirmation** order must be maintained.

### Pros

- Better performance
- Parallel processing

### Cons

- No global ordering

### Memory Trick

**Only related events are ordered.**

---

# 3. Causal Ordering

## Definition

**Cause must happen before Effect.**

```
A → B
```

If A causes B,

everyone must see A before B.

### Real-World Example

**WhatsApp**

```
Ali

Let's meet at 5

↓

Bob

OK
```

Everyone must see:

```
Let's meet at 5

↓

OK
```

But concurrent messages like:

```
Ali: Hello

Mike: Hi
```

can appear in different orders.

### Pros

- Preserves cause-effect
- Natural user experience

### Cons

- Concurrent events may appear in different orders

### Memory Trick

**Cause before Effect.**

---

# Comparison

| Model | Rule | Example |
|--------|------|---------|
| **Total** | Everyone sees the same order | Banking |
| **Partial** | Only related events are ordered | eCommerce |
| **Causal** | Cause always before Effect | WhatsApp |

---

# 5-Second Interview Answer

- **Total Ordering:** Every replica processes all events in the same global order.
- **Partial Ordering:** Only dependent events are ordered; independent events can run in parallel.
- **Causal Ordering:** Cause-and-effect relationships are preserved, while concurrent events may appear in different orders.


#  What is Consistency?

Consistency defines **when all replicas see the latest data after a write.**

**Simple Formula**

```text
Write → Replicas → Read
```

---

# 1. Strong Consistency ⭐⭐⭐⭐⭐

### Definition

> Every read returns the **latest committed data**.

```text
Write
   ↓
All Replicas Updated
   ↓
Read = Latest Data
```
<img width="758" height="556" alt="image" src="https://github.com/user-attachments/assets/8994e084-db5a-4741-87b4-8848d6eeffa2" />

### Use Cases

- Banking
- Payment Gateway
- Inventory
- Stock Exchange

### Pros

- ✅ No stale data
- ✅ Highest correctness

### Cons

- ❌ High latency
- ❌ High coordination

### Interview Question

**Q:** When should we use Strong Consistency?

**A:** When incorrect or stale data cannot be tolerated (e.g., banking or inventory).

---

# 2. Causal Consistency ⭐⭐⭐⭐

### Definition

> Cause always happens before Effect.

```text
Message
   ↓
Reply
```

Example: Suppose Alice updates her email address in an app. This update is a write operation to the data store. Right after that, she tries to log in using the new email, which triggers a read operation that depends on the earlier write.
With causal consistency, the system guarantees that the replica serving Alice’s login request has already applied the email update. As a result, Alice can always view her updated email and log in successfully, as illustrated in the slides below. Without causal consistency, her request may be served by a replica that has not yet applied the update, causing the login to fail.

<img width="921" height="659" alt="image" src="https://github.com/user-attachments/assets/489e65a0-12ba-40c3-b897-b03585ab9982" />
<img width="944" height="726" alt="image" src="https://github.com/user-attachments/assets/7e997c30-1527-4af6-bdb5-fe238edd814c" />
<img width="943" height="718" alt="image" src="https://github.com/user-attachments/assets/cf417e8f-9f0e-4777-8fd3-57d5b294f8aa" />
<img width="946" height="722" alt="image" src="https://github.com/user-attachments/assets/51893f6e-13b9-4272-890f-565160e0347f" />
<img width="944" height="716" alt="image" src="https://github.com/user-attachments/assets/c86072b3-4a29-49f3-9bb0-a1b4f3f4c435" />
<img width="958" height="729" alt="image" src="https://github.com/user-attachments/assets/91451ca3-a8a2-4fe4-bbe9-b2742b63b3d0" />

### Use Cases

- WhatsApp
- Slack
- Google Docs

### Pros

- ✅ Preserves dependencies
- ✅ Better performance than Strong

### Cons

- ❌ No global ordering

### Interview Question

**Q:** Why does WhatsApp use Causal Consistency?

**A:** Because replies must always appear after the original message.

---

# 3. Eventual Consistency ⭐⭐⭐⭐⭐

### Definition

> All replicas become consistent **eventually**.
Eventual consistency is the weakest consistency model in distributed databases.

It guarantees that, if no new updates are made to a piece of data, all replicas will eventually converge to the same value. Unlike stronger models, it does not guarantee that reads immediately reflect the latest write, allowing temporary inconsistencies across replicas, as illustrated below.
<img width="665" height="397" alt="image" src="https://github.com/user-attachments/assets/c9684b59-9ea4-4936-8cd0-92b6b15a9324" />


```text
Write
   ↓
Replica A ✅
Replica B ❌
Replica C ❌

↓

Eventually

↓

Replica A ✅
Replica B ✅
Replica C ✅
```

### Use Cases

- Facebook
- Instagram
- DNS
- CDN

### Pros

- ✅ Highest availability
- ✅ Fastest
- ✅ Highly scalable

### Cons

- ❌ Temporary stale data

### Interview Question

**Q:** Why does Facebook use Eventual Consistency?

**A:** Because high availability and performance are more important than immediate consistency.

---

#  Comparison

| Feature | Strong | Causal | Eventual |
|----------|---------|---------|-----------|
| Latest Read | ✅ | Dependent Only | ❌ |
| Global Order | ✅ | ❌ | ❌ |
| Performance | Low | Medium | High |
| Availability | Low | High | Highest |
| Example | Banking | WhatsApp | Facebook |

---

#  Easy Memory Trick

| Model | Remember |
|--------|----------|
| **Strong** | Latest Data |
| **Causal** | Cause → Effect |
| **Eventual** | Eventually Same Data |

---

#  30-Second Interview Answer

> **Strong Consistency** guarantees every read returns the latest value and is used in banking and payment systems. **Causal Consistency** preserves cause-and-effect relationships and is ideal for messaging applications like WhatsApp. **Eventual Consistency** allows temporary stale data but ensures all replicas eventually synchronize, making it suitable for social media, DNS, and CDN systems.

---

#  Golden Interview Rule

- 🏦 **Banking** → Strong Consistency
- 💬 **WhatsApp** → Causal Consistency
- 📱 **Facebook** → Eventual Consistency
