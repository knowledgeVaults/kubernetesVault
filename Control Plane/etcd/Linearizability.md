**[[Linearizability]]** is the strongest consistency guarantee for distributed systems, ensuring that every read reflects the most recent completed write as if operations occurred instantaneously on a single machine

* [[etcd]] provides linearizable reads and writes by default through its [[Control Plane/etcd/Leader Election]]-based architecture
* Every read is served through the leader, which guarantees the response reflects all committed writes
* Makes etcd behave like a single, perfectly consistent key-value store despite being distributed

## How etcd Achieves Linearizability

### Linearizable Writes

All writes go through Raft log replication before being acknowledged to the client

1. Client sends a write to the etcd leader
2. The leader replicates the entry to a quorum of followers
3. Once committed by the quorum, the leader acknowledges the write
4. The client receives the response only after the write is durably committed

### Linearizable Reads (default)

By default, etcd reads are **linearizable** and are routed through the leader

* The leader confirms it is still the current leader (via a quorum check) before serving the read
* Prevents stale reads from a leader that has been partitioned and doesn't know it has been replaced

### Serializable Reads (optional, lower latency)

```Bash
etcdctl get /mykey --consistency=s
```

* Served from the local node without a quorum check and may return slightly stale data
* Useful for read-heavy workloads where slight staleness is acceptable in exchange for lower latency

## CAP Trade-Off

etcd's linearizability comes at the cost of **availability during partitions**

* If the leader cannot form a quorum, it stops serving writes (and linearizable reads)
* This is an intentional choice: correctness over availability per the CAP theorem

## Practical Impact in Kubernetes

All Kubernetes [[kube-apiserver]] reads from etcd use linearizable reads by default

* Ensures `kubectl get` always returns the current state of the cluster
* The API server's watch cache (in-memory) may serve reads from cache after initial sync, but etcd writes are always linearizable
* Linearizability is what makes Kubernetes leader election via Lease objects reliable
