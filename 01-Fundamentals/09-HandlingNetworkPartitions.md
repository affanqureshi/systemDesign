#  Handling Network Partitions and System Failures – 


A network partition occurs when servers in a distributed system cannot communicate due to network failures, even though the servers themselves are still running. This is a common problem in distributed systems and must be handled during system design.

According to the CAP Theorem, during a network partition a system can guarantee either Consistency (C) or Availability (A), but not both, while always maintaining Partition Tolerance (P).

Consistency (CP): All users always see the latest data. During a partition, the system may reject reads or writes to keep data correct.
Availability (AP): The system continues accepting requests even during a partition, but some users may receive outdated (stale) data.
Example

Suppose a bank has two database servers.

Initial balance = $1000 on both servers.
A network partition occurs.
A customer deposits $500 on Server A.
Server B cannot receive the update.

Now the system has two choices:

CP: Server B rejects requests until synchronization is restored. Users always see correct data.
AP: Server B continues serving requests and shows $1000 until synchronization happens later (eventual consistency).
Real-World Systems
MongoDB: Uses replica-set elections and quorum. It is a CP system.
Redis Sentinel: Uses quorum to elect a new primary and avoid multiple leaders.
Consul: Uses the Raft consensus algorithm and is mainly CP.
Interview Takeaway
Banking, payments, inventory: Choose CP (correctness is critical).
Social media, chat feeds, news feeds: Choose AP (availability is more important).
