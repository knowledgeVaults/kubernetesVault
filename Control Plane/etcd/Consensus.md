
**[[Consensus]]** in distributed systems is the process by which multiple nodes agree on a single value or decision, even in the presence of failures, delays, or network partitions

* Ensures that every participating node that completes the process agrees on the same result
* Critical for distributed databases and coordination services like [[etcd]], ZooKeeper, and Consul
* etcd uses the **[[Control Plane/etcd/Leader Election]]** consensus algorithm to achieve agreement across cluster members

## Why Consensus Is Hard

In a distributed system, nodes cannot always communicate reliably

* **Network partitions**: some nodes may be temporarily unreachable from others
* **Node crashes**: a node may fail mid-operation, leaving data in an uncertain state
* **Message delays**: messages can arrive out of order or be duplicated
* Consensus algorithms provide guarantees despite these failure modes

## Raft Consensus in etcd

Raft breaks consensus into three subproblems: **leader election**, **log replication**, and **safety**

### Log Replication

1. All client writes go to the **leader**
2. The leader appends the entry to its local **write-ahead log (WAL)**
3. The leader sends `AppendEntries` RPCs to all followers in parallel
4. Once a **quorum** of nodes acknowledge the entry, the leader **commits** it
5. The leader applies the committed entry to the state machine (etcd's key-value store) and responds to the client
6. Followers apply the entry to their own state machines on the next heartbeat

### Safety Guarantee

Raft guarantees that once an entry is committed, it will never be lost even if the leader crashes immediately after committing

* A new leader can only be elected if it has the most up-to-date log among a quorum
* This prevents a node with a stale log from becoming leader and overwriting committed entries

## CAP Theorem and etcd

etcd prioritises **Consistency** and **Partition Tolerance** (CP) over Availability

* During a network partition where quorum is lost, etcd stops accepting writes
* This ensures no stale reads or split-brain writes at the cost of temporary unavailability
* Reads can optionally be linearised (routed through the leader) or served from followers (less consistent but faster)
