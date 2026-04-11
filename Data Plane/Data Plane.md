The **Data Plane** (also called the Worker Plane) is the layer of a Kubernetes cluster responsible for running containerized workloads and handling actual data traffic as directed by the Control Plane

* Operates on **worker nodes** where each node runs a set of essential agents and the [[Container Runtime]]
* Receives scheduling decisions from the Control Plane and executes them by starting, stopping, and monitoring containers
* Reports pod and node health back to the Control Plane via the [[kubelet]]

## Data Plane Components

| Component             | Purpose                                                                                          |
| --------------------- | ------------------------------------------------------------------------------------------------ |
| [[kubelet]]           | Node agent; receives PodSpecs from the [[kube-apiserver]] and manages pod lifecycle on the node  |
| [[kube-proxy]]        | Programs network rules (iptables/IPVS) on each node to route Service traffic to the correct pods |
| [[Container Runtime]] | Executes containers; communicates with the kubelet via the CRI (e.g., [[containerd]], CRI-O)     |

## Node Architecture

```
          ┌─────────────────────────────────┐
          │           Worker Node           │
          │                                 │
          │  kubelet  ◄──── API Server      │
          │     │                           │
          │     ▼                           │
          │  containerd ──► runc            │
          │     │                           │
          │  kube-proxy (iptables/IPVS)     │
          │     │                           │
          │  CNI Plugin (Pod networking)    │
          └─────────────────────────────────┘
```

## Node Registration

When a worker node starts, the kubelet self-registers with the API server

* Creates a `Node` object with capacity (CPU, memory, ephemeral storage) and allocatable resources
* Reports the node's labels, conditions, and container runtime version
* The Node Controller on the Control Plane monitors the node's liveness via Lease objects

## Workload Execution Flow

1. Scheduler assigns a Pod to a node (writes `spec.nodeName`)
2. kubelet detects the assignment via its API server watch
3. kubelet instructs the container runtime to pull the image and start containers
4. kubelet mounts volumes, injects environment variables, and configures readiness/liveness probes
5. kube-proxy updates iptables rules if new Services reference the pod

## Node Resource Allocation

