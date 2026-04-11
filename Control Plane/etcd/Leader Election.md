**[[etcd]] [[Control Plane/etcd/Leader Election|Leader Election]]** is the process by which etcd cluster members select a single leader node responsible for processing all write operations and coordinating replication via the Raft [[Consensus]] algorithm

* etcd uses the **Raft** distributed consensus protocol for leader election
* All client writes go to the leader; the leader replicates entries to followers before committing
* If the leader becomes unreachable, followers initiate a new election after an election timeout

## Raft Election Quorum

```
Quorum = floor(Number_of_nodes / 2) + 1
```

| Cluster Size | Quorum | Fault Tolerance |
|---|---|---|
| 1 | 1 | 0 nodes |
| 3 | 2 | 1 node |
| 5 | 3 | 2 nodes |
| 7 | 4 | 3 nodes |

A cluster can only elect a new leader if a majority (quorum) of nodes are reachable.

## Election Process

1. Each follower has a randomised **election timeout** (150ms–300ms by default)
2. When a follower does not hear from the leader within its timeout, it increments its **term** number and transitions to **candidate** state
3. The candidate sends `RequestVote` RPCs to all other nodes
4. If a candidate receives votes from a quorum, it becomes the new **leader**
5. The new leader immediately begins sending heartbeats to prevent further elections

## Term Numbers

Each Raft term is a monotonically increasing integer

* A node will not vote for a candidate with a lower term than its own
* Term numbers are stored persistently so they survive restarts
* Any node receiving a message with a higher term immediately updates to that term and steps down to follower state

## Viewing the Current Leader

```Bash
etcdctl endpoint status --write-out=table \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
# IS LEADER column shows true for the current leader
```

## Impact on Kubernetes Control Plane

[[kube-apiserver]] connects to all etcd members and routes writes to the leader automatically via the etcd client library

* If the etcd leader changes, the client transparently reconnects to the new leader
* Control plane disruption during leader election is typically under 1 second in healthy clusters
* Raft election timeouts are tunable: `--heartbeat-interval` and `--election-timeout` on the etcd server
