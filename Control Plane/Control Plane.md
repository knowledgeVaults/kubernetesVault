The **Control Plane** is the management layer of a Kubernetes cluster 

* Runs on dedicated **control plane nodes** (master nodes) that are separated from worker nodes in production
* All control plane components communicate through the **[[kube-apiserver]]** as the single source of truth
* In high-availability setups, control plane components run with [[Control Plane/etcd/Leader Election|Leader Election]] across multiple nodes

## Core Components

| Component                    | Purpose                                                                     |
| ---------------------------- | --------------------------------------------------------------------------- |
| `kube-apiserver`             | Exposes the Kubernetes REST API; the entry point for all cluster operations |
| [[etcd]]                     | Distributed key-value store; persists all cluster state                     |
| [[kube-scheduler]]           | Assigns unscheduled Pods to suitable nodes                                  |
| [[kube-controller-manager]]  | Runs all core controllers (Node, [[Deployments]], [[ReplicaSets]], etc.)    |
| [[cloud-controller-manager]] | Integrates with cloud provider APIs (AWS, GCP, Azure)                       |
| [[CoreDNS]]                  | Provides cluster-internal DNS service discovery                             |

## Communication Flow

```
kubectl / External Client
        │
        ▼
 kube-apiserver (HTTPS :6443)
    │         │         │
    ▼         ▼         ▼
  etcd   kube-scheduler  kube-controller-manager
```

All components talk to etcd exclusively via the kube-apiserver — they never connect to etcd directly.

## Control Plane Node Requirements

* At least 2 CPUs and 2 GB RAM per control plane node
* Low-latency network to etcd nodes (< 10ms RTT recommended)
* An odd number of control plane nodes (3 or 5) for etcd quorum

## Checking Control Plane Health

```Bash
kubectl get pods -n kube-system
kubectl get componentstatuses   # Deprecated in 1.19+; may still work
kubectl cluster-info

# Check individual components
kubectl logs -n kube-system kube-apiserver-NODENAME
kubectl logs -n kube-system kube-controller-manager-NODENAME
kubectl logs -n kube-system kube-scheduler-NODENAME
```

## High Availability Setup

For HA, run 3 or 5 control plane nodes behind a load balancer

* etcd quorum requires majority: 3 nodes tolerate 1 failure; 5 nodes tolerate 2 failures
* kube-apiserver is stateless where all instances can serve requests simultaneously
* kube-scheduler and kube-controller-manager use leader election where only one active at a time
