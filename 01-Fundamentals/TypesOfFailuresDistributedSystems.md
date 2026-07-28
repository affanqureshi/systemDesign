
# Types of Failures in Distributed Systems 
<img width="904" height="553" alt="image" src="https://github.com/user-attachments/assets/28b7ecf9-1449-4744-92c3-52c4ed28c313" />

# Why Failures Happen

- Single server = **Single Point of Failure (SPOF)** ❌
- Distributed systems use many machines.
- More machines = More chances of failure.
- Failure is **expected**, not exceptional.
- Goal is **Fault Tolerance**, not failure prevention.

---

# Types of Failures

```text
Distributed System
        │
 ┌──────┼────────┐
 │      │        │
Hardware Software Network
Failure  Failure  Failure
```

---

# 1. Hardware Failure 🖥️

## What is it?

Physical component stops working.
These failures can be understood as occurring across several hierarchical levels in a distributed system, ranging from individual components up to entire data centers. The pyramid below illustrates this layered nature of hardware failures, where each lower layer represents a broader level of potential disruption.
<img width="831" height="480" alt="image" src="https://github.com/user-attachments/assets/c8511bb9-4391-4616-a230-adaa239a5d4a" />

### Examples

- Hard Disk Crash
- RAM Failure
- CPU Failure
- Power Failure
- Network Card Failure
- Rack Failure
- Data Center Failure

---

## Symptoms

- Server offline
- Disk I/O errors
- Data corruption
- Machine unreachable

---

## Recovery

✅ Replication

```text
Server A ❌
      │
      ▼
Replica Server B ✅
```

---

✅ Redundancy

- RAID
- UPS
- Backup Generator
- Multiple Servers

---

✅ Automatic Failover

```text
Load Balancer

Server A ❌
Server B ✅ ← Traffic moves here
```

---

## Real World Example

AWS runs applications across multiple **Availability Zones**.

If one data center fails,

→ Traffic automatically shifts to another.

---

# Interview Question

### Q. How do we recover from hardware failure?

**Answer**

- Replication
- Redundancy
- Failover

---

# 2. Software Failure 💻

## What is it?

Application code or configuration causes failure.
<img width="704" height="471" alt="image" src="https://github.com/user-attachments/assets/2f961aa5-c21c-4e93-8f39-46b97b3e8e53" />

---

## Examples

- Process Crash
- Memory Leak
- Deadlock
- Logic Bug
- Configuration Error

---

## Symptoms

- Wrong Results
- Service Crash
- High CPU
- High Memory
- Slow Response

---

# Cascading Failure

One service failure causes multiple services to fail.

```text
Client
   │
Checkout
   │
Payment
   │
Fraud Service ❌

↓

Payment fails

↓

Checkout fails

↓

Customer cannot order
```

---

# Prevention

## Circuit Breaker Pattern

Instead of continuously calling a failing service,

the circuit opens temporarily.

```text
Normal

Client
   │
Payment
   │
Fraud

↓

Failures

↓

Circuit Opens

↓

No Calls Sent

↓

Service Recovers

↓

Half Open

↓

Normal Again
```

---

# Observability

Detect failures using

- Prometheus (Metrics)
- Jaeger (Tracing)
- Centralized Logs

---

# Interview Question

### Q. What is Cascading Failure?

A failure in one service spreads to dependent services.

---

### Q. Which pattern prevents cascading failure?

Circuit Breaker Pattern

---

# 3. Network Failure 🌐

## What is it?

Nodes cannot communicate properly.

---

## Types

### Network Partition

Cluster splits into multiple isolated groups.

---

### Packet Loss

Packets never reach destination.

---

### High Latency

Packets arrive very late.

---

# Biggest Problem

Node A stops receiving heartbeats.

Can Node A know what happened?

❌ No.

Maybe

- Node B crashed.

OR

- Network is broken.

Both situations look identical.

This uncertainty is the hardest problem in distributed systems.

---

# Failure Detection

## Heartbeats

```text
Node A ←♥← Node B

♥ ♥ ♥ ♥

No heartbeat...

↓

Node A suspects failure
```

---

## Timeouts

If heartbeat doesn't arrive within timeout,

Node A assumes something is wrong.

**Important**

Timeouts only **suspect** failure.

They **cannot confirm** it.

---

# CAP Theorem

During a Network Partition,

System must choose:

---

## Option 1

### Consistency (C)

- Stop writes
- Data remains correct
- Lower availability

---

## Option 2

### Availability (A)

- Continue writes
- System stays online
- Possible inconsistent data

---

## Remember

```text
Network Partition

↓

Choose One

Consistency

OR

Availability

Not Both
```

---

# Comparison Table

| Failure | Cause | Detection | Recovery |
|----------|--------|-----------|----------|
| Hardware | Physical device failure | Monitoring | Replication, Failover |
| Software | Bug, Crash, Memory Leak | Logs, Metrics, Traces | Circuit Breaker, Restart |
| Network | Packet Loss, Partition, Latency | Heartbeats, Timeouts | Retry, Replication, CAP Decision |

---

# Real World Examples

## Hardware

Bank server hard disk crashes.

➡ Replica server becomes primary.

---

## Software

Payment service has memory leak.

➡ Service crashes.

➡ Checkout service also starts failing.

---

## Network

Karachi data center loses connection with Lahore.

Both continue working independently.

Later data conflicts occur.

---

# 2-Second Revision

- **Hardware Failure** → Physical machine breaks → **Replication + Failover**
- **Software Failure** → Bug or crash → **Circuit Breaker + Monitoring**
- **Network Failure** → Communication breaks → **Heartbeats + Timeouts + CAP Theorem**

---

# Interview Questions

## Easy

**Q1. What are the three types of failures?**

- Hardware
- Software
- Network

---

**Q2. What is hardware failure?**

Failure of physical components like disk, RAM or power supply.

---

**Q3. What is software failure?**

Failure caused by bugs, crashes or resource exhaustion.

---

**Q4. What is network failure?**

Failure in communication between distributed nodes.

---

## Medium

**Q5. Why are network failures difficult?**

Because a node cannot determine whether another node has crashed or is simply unreachable due to a network issue.

---

**Q6. What causes cascading failure?**

One failed service causes dependent services to fail.

---

**Q7. Which pattern prevents cascading failure?**

Circuit Breaker Pattern.

---

**Q8. How do we detect node failures?**

- Heartbeats
- Timeouts

---

**Q9. What happens during a network partition?**

The system must choose between:

- Consistency
- Availability

(CAP Theorem)

---

# Key Takeaways

- Distributed systems are designed assuming **failures will happen**.
- Hardware failures are handled using **Replication, Redundancy and Failover**.
- Software failures are isolated using **Circuit Breakers and Observability**.
- Network failures are the hardest because nodes cannot distinguish **crash vs communication failure**.
- During a partition, **CAP Theorem** forces a choice between **Consistency** and **Availability**.
