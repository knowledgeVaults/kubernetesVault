
Ask
* What's the purpose of this cluster?
* What's the cloud adoption? (Cloud or on-prem?)
* What kind of workloads will be run on the cluster?

Example:
* For education purposes: [[minikube]] or single-node cluster with [[kubeadm]]/GCP/AWS
* For development and testing: Multi-node cluster with a Single master and multiple worker nodes set up using kubeadm or on cloud
* For production applications: Highly-available multi-node cluster with multiple master nodes

Storage:
* For high performance: SSD-backed storage
* For multiple concurrent connections: Network-based storage
* Persistent shared volumes for shared access across multiple Pods
* Label nodes with specific disk types
* Use node selectors to assign applications to nodes with specific disk types

## Control Plane High Availability

Run an **odd number of control plane nodes** (3 or 5) for [[etcd]] quorum

* 3-node cluster: tolerates 1 node failure
* 5-node cluster: tolerates 2 node failures
* Place control plane nodes across different availability zones for fault tolerance
* Use a load balancer in front of the API servers (e.g., AWS NLB, HAProxy, keepalived)

## Separate etcd Topology

For large clusters, consider **external etcd** by running etcd on dedicated nodes separate from the [[kube-apiserver]]

* Isolates etcd storage and I/O from API server load
* Allows independent scaling and tuning of etcd
* Increases node count required but improves stability

## Node Sizing

| Role | Minimum | Recommended Production |
|---|---|---|
| Control plane | 2 CPU, 2 GB RAM | 4 CPU, 8 GB RAM |
| Worker node | 2 CPU, 4 GB RAM | Depends on workload |
| etcd (external) | 2 CPU, 8 GB RAM | SSD required |

## Node Groups / Node Pools

Separate nodes into groups by workload type

* **System node pool**: control plane and system DaemonSets only ([[Taints]]: `CriticalAddonsOnly`)
* **Application node pool**: general workloads
* **GPU node pool**: ML/AI workloads (taint: `hardware=gpu:NoSchedule`)
* **Spot/preemptible pool**: fault-tolerant batch jobs

## Networking Architecture

* Use separate subnets for control plane and worker nodes
* Restrict API server access (port 6443) to trusted CIDRs via security groups / firewall rules
* Enable audit logging on the API server for compliance
