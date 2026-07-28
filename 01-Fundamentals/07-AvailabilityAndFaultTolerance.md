# Availability and Fault Tolerance in Distributed Systems
High Availability (HA) ensures a system stays online with minimal downtime, while Fault Tolerance (FT) allows it to continue operating even when components fail.
Distributed systems achieve resilience using redundancy, load balancing, replication, and automatic recovery/failover.
Key metrics include Availability (uptime), MTBF (Mean Time Between Failures), and MTTR (Mean Time To Repair).
The objective is not to eliminate failures, but to handle them gracefully so users experience little or no service disruption.


# Availability in distributed systems#
Availability refers to the consistency with which a system or service remains operational and accessible to users when needed.

In simple terms, it measures uptime, which represents the percentage of time a system functions without interruption. High availability focuses on minimizing downtime to ensure that users can always access the service, as illustrated below.
<img width="937" height="468" alt="image" src="https://github.com/user-attachments/assets/9d87a3e3-58b8-444a-af25-2d2d9a7a22e2" />


<img width="1174" height="607" alt="image" src="https://github.com/user-attachments/assets/455d6419-e7e0-4adf-800f-6bb541a72b2a" />





# 1. High Availability (HA)

High Availability means the system remains online with **minimum downtime**.

### How to achieve HA?
- Redundancy
- Load Balancer
- Replication
- Health Checks
- Failover

### Real World ()
**Easypaisa/JazzCash Payment**

Agar Server A crash ho jaye,
Load Balancer automatically request **Server B** ko bhej deta hai.

Customer ko pata bhi nahi chalta ke backend mein failure hua tha.

---

# 2. Redundancy

## Definition
Extra copies of servers, databases, or services rakhna taake ek fail ho to doosra kaam kare.

### Purpose
Remove **Single Point of Failure (SPOF).**

### Types
- Physical (Multiple Servers, AZs, Regions)
- Logical (Multiple Containers/Pods)

###  Example

Netflix ki movie Server A se aa rahi thi.

Server A down ho gaya.

Server B same movie serve karna start kar deta hai.

User ki movie stop nahi hoti.

---

# 3. Redundancy Models

## A. Active-Passive

One server works.

Second server standby hota hai.

Failure ke baad standby active ho jata hai.

```
Users
   |
Primary Server ✅

Standby Server 😴
```

### Pros
- Easy
- Strong consistency

### Cons
- Standby idle rehta hai
- Failover mein thora delay

### Real World ()

Primary Database Karachi mein chal rahi hai.

Karachi Data Center down.

Lahore standby database active ho gayi.

Customer service continue.

---

## B. Active-Active

Sab servers ek sath traffic handle karte hain.

Load Balancer requests distribute karta hai.

```
        Users
          |
    Load Balancer
     /    |    \
   S1    S2    S3
```

### Pros
- Better performance
- No downtime
- Easy scaling

### Cons
- Complex consistency

### Real World ()

Daraz Sale Day

1 million users website open karte hain.

Load Balancer traffic multiple servers mein divide karta hai.

Agar ek server down ho jaye to baqi servers traffic handle karte hain.

Customer ko error nahi milta.

---

# 4. Health Checks

Server ki health continuously check ki jati hai.

Agar unhealthy ho to traffic band.

###  Example

Load Balancer har 10 sec baad ping karta hai.

Server reply nahi karta.

Load Balancer usko remove kar deta hai.

Traffic doosre server pe chali jati hai.

---

# 5. Failover

Automatic switching to backup server.

###  Example

ATM backend crash.

Backup server automatically active.

Customer withdrawal continue.

---

# 6. Replication

Same data multiple databases mein store hota hai.

### Purpose

- Prevent Data Loss
- High Availability

###  Example

Meezan Bank account balance Karachi aur Lahore dono databases mein save hai.

Karachi database down.

Lahore replica se balance mil jata hai.

---

# 7. Fault Tolerance (FT)

## Definition

System failure ke baad bhi correctly kaam karta rahe.

### Design Principles

### Failure Detection
Find failed server.

### Isolation
Failed component ko system se alag karna.

### Recovery
Automatically restart ya replace.

### Self Healing
System khud recover kare.

---

###  Example

WhatsApp server crash.

Kubernetes new container start kar deta hai.

User sirf 1-2 second delay feel karta hai.

---

# 8. Consensus

Distributed nodes agree before writing data.

Mostly:

- Raft
- Paxos

###  Example

Bank transfer

5 database nodes hain.

Kam az kam 3 agree karein.

Tab transaction commit hogi.

Agar sirf 2 agree karein

Transaction reject.

---

# 9. Quorum

Majority agreement.

Formula

```
N/2 + 1
```

5 Nodes

Need

3 Votes

---

###  Example

5 ATM servers.

3 bolte hain balance Rs 10,000.

2 bolte hain Rs 9,000.

Majority (3) accept hogi.

---

# 10. Recovery

Failed node dubara system join karta hai.

Latest data sync karta hai.

###  Example

Server 2 hours offline tha.

Restart ke baad missing transactions sync kar leta hai.

---

# 11. Network Partition

Network break ho jata hai.

Servers ek dusre se communicate nahi kar pate.

###  Example

Karachi aur Lahore data centers ka network cut gaya.

Dono ek dusre se baat nahi kar pa rahe.

Ye Network Partition hai.

---

# 12. Split Brain

Network partition ke baad dono sides apne aap ko leader samajhne lagti hain.

Result

Same data pe different writes.

Data corruption.

###  Example

Bank database ka Karachi leader bhi active.

Lahore bhi khud ko leader bana leta hai.

Dono account balance update karte hain.

Customer ka balance different ho sakta hai.

---

# 13. Fencing

Old leader ko system se block kar dena.

Sirf new leader writes accept kare.

<img width="753" height="435" alt="image" src="https://github.com/user-attachments/assets/d943d138-a34c-4d03-882c-bcb75b5bd763" />


###  Example

Karachi old leader reconnect hua.

System usko write permission nahi deta.

Sirf Lahore leader updates karega.

---

# 14. Important Metrics

Availability → Uptime %

MTBF → Mean Time Between Failures

MTTR → Mean Time To Repair

Goal:

↑ MTBF

↓ MTTR

---

# Trade-offs

| Active-Passive | Active-Active |
|---------------|---------------|
| Simple | Complex |
| Lower Cost | Higher Cost |
| Idle Backup | Better Resource Usage |
| Small Failover Delay | Near Zero Downtime |

---
#  Case study: Highly available and fault-tolerant system
Let’s integrate the principles of high availability and fault tolerance into a practical scenario: designing a web-scale e-commerce checkout service that must be always-on.

First, for high availability, we would deploy the service across at least three different availability zones in an active-active configuration. A global load balancer would distribute incoming traffic, and regional load balancers would manage traffic within each zone.

Health checks would constantly monitor the service instances.

When an instance fails, its load balancer stops sending traffic to it, but if an entire availability zone goes down, the global load balancer reroutes traffic to the remaining healthy zones. The illustration below shows the high-level architecture, detailing how traffic flows through the global and regional load balancers and how redundancy is maintained across availability zones.
<img width="884" height="625" alt="image" src="https://github.com/user-attachments/assets/f8351445-2ba6-4330-b39d-20b2cc5acf97" />
For fault tolerance, the service’s database would need to be resilient.

We could use a distributed database like Amazon Aurora or Google Cloud Spanner, which replicates data synchronously across multiple availability zones, as illustrated below. In such a setup, write operations require a quorum of nodes to acknowledge the request before it is confirmed to the user.

This ensures that even if one database node fails, the data remains safe and the system can continue processing transactions using the replicas.
<img width="1085" height="470" alt="image" src="https://github.com/user-attachments/assets/2aa8a6e3-801f-4c19-81c1-39f8e8789f16" />

Note: A common pitfall is focusing only on server redundancy while neglecting dependencies. If our highly available checkout service relies on a single, non-redundant payment processor API, we still have a single point of failure. True resilience requires analyzing the entire dependency chain.

This layered approach combines strategies at different levels of the stack.

Redundancy at the application layer provides uptime, while replication and consensus at the data layer provide correctness and data safety. The key is to select the right trade-offs based on business needs. For a product catalog, eventual consistency from asynchronous replication might be acceptable.

Whereas, for a payment service, strong consistency from synchronous replication is non-negotiable.

# Test Your Knowledge!

If a system uses an active-active deployment across three availability zones, how should the load balancers respond if two zones go down simultaneously? What are potential risks? Enter your answer in the widget below.

Solution 
Load balancer check the service is working or not . if service is down all traffic will be re route into available service .


risk is higher load on a single service . api response time will be increase and, in systems relying on synchronous replication, possible impacts on data consistency or write availability. Keep exploring!

# Interview Questions

### Q1. Difference between HA and FT?

HA → System online rehta hai.

FT → Failure ke baad bhi system kaam karta rehta hai.

---

### Q2. Active-Passive vs Active-Active?

Active-Passive:
1 Active
1 Standby

Active-Active:
Sab servers active.

---

### Q3. Why Replication?

- Prevent Data Loss
- High Availability
- Faster Recovery

---

### Q4. What is Split Brain?

Do leaders ek hi waqt writes accept karne lagen.

---

### Q5. How to prevent Split Brain?

- Quorum
- Consensus (Raft/Paxos)
- Fencing

---

# 2-Second Revision

✅ HA → Stay Online

✅ FT → Keep Working After Failure

✅ Redundancy → Extra Servers

✅ Active-Passive → Backup Server

✅ Active-Active → All Servers Active

✅ Health Check → Detect Failure

✅ Failover → Switch to Backup

✅ Replication → Multiple Data Copies

✅ Consensus → Majority Decision

✅ Quorum → N/2 + 1 Votes

✅ Split Brain → Two Leaders

✅ Fencing → Block Old Leader

✅ Recovery → Sync & Rejoin
