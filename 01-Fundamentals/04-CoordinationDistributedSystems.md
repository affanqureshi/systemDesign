# Coordination in Distributed Systems


# What is Coordination?

**Definition**

Coordination ka matlab hai multiple servers/services ko is tarah manage karna ke:

- Sab same data/state par agree karein.
- Duplicate work na ho.
- Conflicting updates na hon.
- System reliable aur consistent rahe.

**One Line Interview Answer**> 
Coordination multiple distributed nodes ko synchronize karta hai taake wo ek hi state aur decision par agree karein.

# Why Coordination?

Without a reliable way to coordinate, the system can run into serious problems:

- ❌ Multiple servers same data update kar dete hain.
- ❌ Duplicate job execute hoti hai.
- ❌ Multiple leaders ban jate hain.
- ❌ Data inconsistent ho jata hai.
- ❌ Down server detect nahi hota.



It may not know which machine should handle certain tasks.

Different machines might overwrite each other’s data.

Some machines might not even realize when others have been disconnected or stopped working.

This challenge is called the coordination problem.

It sits at the core of distributed systems and frequently appears in System Design interviews because it directly impacts a system’s reliability and correctness. In this lesson, we’ll examine the key building blocks that enable distributed services to coordinate, transforming a set of independent machines into a powerful, unified system.

# Introduction to distributed system coordination#
<img width="686" height="596" alt="image" src="https://github.com/user-attachments/assets/bd46d419-84d0-4264-840f-c24e90a695a1" />


Breaking applications into distributed services improves scalability and fault tolerance, but services must coordinate with each other.
Coordination means multiple nodes agree on a shared state or action (e.g., leader election, preventing duplicate job processing).
Systems use state replication and heartbeats to keep data consistent and detect failed nodes.
During network partitions, the CAP theorem forces a trade-off between Consistency and Availability.
Coordination is achieved using coordination primitives such as leader election, consensus, distributed locks, and shared resource management.

# Key primitives for coordination across services#


<img width="854" height="656" alt="image" src="https://github.com/user-attachments/assets/ee34773b-23cf-409b-9c1b-5c098776969c" />


## 1. Leader Election

In many distributed systems, it is often simpler and more efficient to designate a single node—called a leader—to handle specific responsibilities.

These duties might include managing write operations in a database, coordinating tasks across worker nodes, or maintaining cluster metadata. Leader election is the process through which nodes in a cluster coordinate to select a leader.

If the current leader fails, the remaining nodes must elect a new one to keep the system running smoothly. Classic algorithms such as the Bully and Ring algorithms provide formal, well-studied methods for performing this election.
### Kya hai?

Cluster decide karta hai

> Leader kaun hoga?

Leader handle karta hai

- Write Requests
- Metadata
- Cluster Coordination

Agar leader fail ho jaye

Automatically new leader elect hota hai.

---

### Example

Database Cluster

```
Server A

Server B

Server C
```

Current Leader

```
Server B
```

Agar

```
Server B Down
```

To

```
Server C
```

Leader ban jayega.

---

### Banking Example

3 Core Banking Servers hain.

Sab account update nahi kar sakte.

Sirf leader server balance update karega.

---

### Interview Answer

Leader Election ek process hai jisme cluster decide karta hai ke ek hi node leader hogi jo write operations aur coordination handle karegi. Leader fail ho to automatically naya leader elect ho jata hai.

---

## Problem

Multiple servers perform the same critical task.

Example:
- Multiple DB masters accepting writes
- Multiple schedulers running the same job
- Multiple payment processors settling one transaction

Result:
- ❌ Duplicate work
- ❌ Data corruption
- ❌ Split Brain

---

## Solution

Elect **one Leader**.

- Leader handles writes/critical tasks.
- Others become Followers.
- If Leader fails, Followers elect a new Leader.

```
        Leader
           |
   -----------------
   |       |       |
Follower Follower Follower
```

---

## Internal Working

1. All nodes start.
2. Nodes exchange heartbeats.
3. Election begins if no leader exists.
4. Majority votes for one node.
5. Winner becomes Leader.
6. Leader continuously sends heartbeats.
7. If heartbeat stops → new election.

Protocols:
- Raft
- Paxos
- ZooKeeper (ZAB)




# 2. Distributed Locks

## Problem

Inventory Service

Only **1 iPhone** left.

1000 users click **Buy** simultaneously.

Without locking:

```
Stock = 1

Server A reads 1
Server B reads 1
Server C reads 1
```

All update stock.

Result:

```
Stock = -999
```

Overselling occurs.

---

## Solution

Allow only one server to modify data at a time.

```
Acquire Lock

↓

Update Inventory

↓

Release Lock
```

Others wait or retry.

---

## Internal Working

Application asks Lock Service

```
Acquire Lock(Product-123)
```

Lock Service (Redis / ZooKeeper / etcd)

returns

```
Lock Granted
```

Other requests receive

```
Lock Busy
```

Retry later.

---

## Types

### Pessimistic Lock

- Lock first
- Then update

Pros
- Highest consistency

Cons
- Lower throughput

---

### Optimistic Lock

- No lock initially
- Check Version Number before update
- Retry if version changed

Pros
- High throughput

Cons
- Retry required during conflicts

---

## Real World Example

Bank Transfer

```
Acquire Lock(Account)

↓

Debit

↓

Commit

↓

Release Lock
```

---

## Interview Answer

> Distributed Locks guarantee that only one server updates shared resources at a time, preventing race conditions and duplicate updates.

---

# 3. Consensus Protocol

## Problem

Five servers store the same data.

Leader sends:

```
Balance = £500
```

Network fails.

Only two servers receive it.

Three don't.

Which value is correct?

---

## Solution

Use **Majority Voting**.

```
5 Servers

Need 3 Votes

↓

Commit
```

If majority isn't achieved,

No commit happens.

---

## Internal Working (Raft)

```
Leader

↓

Propose Log Entry

↓

Followers ACK

↓

Majority ACK

↓

Commit

↓

Apply to Database
```

Example

```
SET Balance = £500

Node1 ✓

Node2 ✓

Node3 ✓

Majority

Committed
```

---

## Popular Protocols

- Raft
- Paxos
- Multi-Paxos
- ZAB

---

## Used In

- Kafka (KRaft)
- etcd
- ZooKeeper
- Consul
- CockroachDB

---

## Interview Answer

> Consensus ensures all healthy nodes agree on the same value. Data is committed only after a majority acknowledges it.

---

# 4. Service Discovery

## Problem

Microservices run on dynamic IPs.

Today

```
Payment Service

10.1.1.10
```

Tomorrow

```
10.1.5.18
```

Hardcoding IPs breaks communication.

---

## Solution

Use a **Service Registry**.

```
Order Service

↓

Ask Registry

↓

Where is Payment Service?

↓

Registry returns address

↓

Call Payment Service
```

---

## Internal Working

Service starts

↓

Registers itself

```
Payment Service

10.1.5.18
```

↓

Sends heartbeat periodically.

↓

If heartbeat stops,

Registry removes it.

↓

Clients always receive healthy instances.

---

## Popular Tools

- Eureka
- Consul
- Kubernetes DNS
- etcd
- ZooKeeper

---

## Load Balancing

Registry returns

```
Payment-1

Payment-2

Payment-3
```

Client selects one using:

- Round Robin
- Least Connections
- Random
- Weighted

---

## Interview Answer

> Service Discovery allows services to find each other dynamically without hardcoded IP addresses. Healthy instances are automatically registered and unhealthy ones are removed.

---

# Architect-Level Comparison

| Primitive | Problem | Solution | Internal Working | Real World Example |
|-----------|----------|----------|------------------|-------------------|
| **Leader Election** | Multiple nodes performing the same task | Elect one Leader | Heartbeats + Voting | Database Primary, Kafka Controller |
| **Distributed Lock** | Concurrent updates | One server updates at a time | Lock → Update → Unlock | Inventory, Bank Account |
| **Consensus** | Nodes disagree on data | Majority agreement | Propose → ACK → Commit | Kafka, etcd, ZooKeeper |
| **Service Discovery** | Dynamic IPs | Registry lookup | Register → Heartbeat → Lookup | Kubernetes, Eureka |

---

# How They Work Together (E-Commerce)

Customer places order

```
Customer

↓

API Gateway

↓

Service Discovery
Find Inventory Service

↓

Distributed Lock
Lock Product

↓

Leader Election
Leader Coordinates Write

↓

Consensus
Replicate Inventory Update

↓

Database Updated

↓

Unlock Product

↓

Order Success
```

---

# Interview Cheat Sheet (Remember This)

| Primitive | One-Line Answer |
|------------|-----------------|
| **Leader Election** | Selects one server to perform critical tasks. |
| **Distributed Lock** | Prevents multiple servers from modifying the same resource simultaneously. |
| **Consensus** | Ensures a majority of nodes agree before data is committed. |
| **Service Discovery** | Helps services find healthy service instances dynamically. |

---
Architecture and design principles#
Apache ZooKeeper organizes its data in a hierarchical structure similar to a traditional file system, with information stored in nodes known as znodes.

It relies on a custom consensus protocol called ZooKeeper Atomic Broadcast (ZAB) to ensure consistent, ordered updates across the cluster. etcd, developed by the CoreOS team, is a distributed key–value store that uses the Raft consensus protocol.

It has become the standard coordination backend for Kubernetes, where it maintains all cluster state and configuration data.

A key feature shared by both systems is their watcher mechanism. A watcher allows a client to “subscribe” to a specific key or node and receive notifications whenever that value changes or is deleted. This enables services to react immediately to updates, making it straightforward to propagate configuration changes or signal events across a distributed system.

<img width="822" height="477" alt="image" src="https://github.com/user-attachments/assets/bd46f447-6381-4e53-bfb6-88ef51a54898" />

""Common use cases""
By combining their primitives, ZooKeeper and etcd make several common coordination tasks straightforward:

Configuration storage: Services can store shared configurations in the system and automatically reload them when a watcher signals a change.

Leader election: Multiple nodes compete to create the same ephemeral key. The node that succeeds becomes the leader. If it fails, the key disappears, and another node can take over.

Lock management: Ephemeral keys can also serve as distributed locks. Only the client that successfully creates the key holds the lock through its active session, preventing conflicts until the lock is released or the session ends.

# Why are coordination tools reliable in production?#

They achieve this by running as a cluster or an ensemble, where a majority of nodes, known as a quorum, must be operational and agree on changes for those changes to take effect. To guarantee that all nodes in the cluster share the same state and make coordinated decisions, these tools rely on consensus algorithms.

These algorithms ensure that even in the presence of failures or network partitions, the cluster can agree on updates and maintain a consistent view of the data across all nodes. As a result, clients interacting with the system always observe a reliable and up-to-date state, enabling the system to function correctly and remain highly available under adverse conditions.

Insight: Coordination services are often run as a separate cluster because their performance characteristics and failure modes differ from those of the main application. Mixing them can lead to situations where an application failure brings down the coordination system it relies on for recovery.
To better understand how these concepts are applied in practice, let’s compare two of the most popular coordination tools, Apache ZooKeeper and etcd, highlighting their key features and differences.
# Distributed job scheduling#
<img width="717" height="436" alt="image" src="https://github.com/user-attachments/assets/46d6a347-7845-4b98-a685-ba46ccd11158" />

# 60-Second Senior Software Engineer Interview Answer

> In distributed systems, coordination primitives ensure multiple servers work as a single logical system. **Leader Election** selects one node to perform critical operations and automatically elects a new leader on failure. **Distributed Locks** prevent concurrent modifications to shared resources such as inventory or account balances. **Consensus Protocols** like Raft ensure a majority of nodes agree before committing data, maintaining consistency during failures. **Service Discovery** enables microservices to locate healthy service instances dynamically through a service registry instead of relying on hardcoded IP addresses. Together, these mechanisms provide scalability, fault tolerance, high availability, and strong consistency in modern distributed architectures.

# Pareto Revision (2 Minutes)

✅ Coordination = Multiple servers ko synchronize karna

✅ Leader Election = Ek leader chooses

✅ Distributed Lock = Sirf ek server access kare

✅ Consensus = Sab agree kare

✅ Service Discovery = Service ka address find kare

✅ ZooKeeper = Kafka/Hadoop

✅ etcd = Kubernetes

✅ Quorum = Majority agree

✅ Watcher = Change notification


