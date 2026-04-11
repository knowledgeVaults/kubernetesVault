**Nodes** are the worker machines in a Kubernetes cluster where containers are scheduled and run

* Each node runs the [[kubelet]], [[kube-proxy]], and a [[Container Runtime]]
* Node objects are registered automatically by the kubelet on startup
* The [[kube-scheduler]] selects nodes for pod placement based on allocatable resources, taints, and affinity rules

## Node Status

```Bash
kubectl get nodes [-o wide]
kubectl describe node NODE_NAME
```

`kubectl get nodes` shows: NAME, STATUS, ROLES, AGE, VERSION

| Status | Meaning |
|---|---|
| `Ready` | Node is healthy and accepting pods |
| `NotReady` | Node is unhealthy (kubelet not reporting) |
| `SchedulingDisabled` | Node is cordoned |

## Node Conditions

```Bash
kubectl get node NODE_NAME -o jsonpath='{.status.conditions[*].type}'
```

| Condition | Meaning |
|---|---|
| `Ready` | Node is healthy |
| `MemoryPressure` | Node is low on memory |
| `DiskPressure` | Node is low on disk |
| `PIDPressure` | Too many processes on the node |
| `NetworkUnavailable` | Network not correctly configured |

## Node Maintenance

```Bash
# Cordon: mark node as unschedulable (no new pods)
kubectl cordon NODE_NAME

# Drain: evict all pods + cordon
kubectl drain NODE_NAME --ignore-daemonsets --delete-emptydir-data

# Uncordon: re-enable scheduling
kubectl uncordon NODE_NAME
```

## Node Labels

```Bash
# Add or update label
kubectl label node NODE_NAME disktype=ssd

# Remove label
kubectl label node NODE_NAME disktype-

# List nodes with a specific label
kubectl get nodes -l disktype=ssd
```

## Node Resource Allocation

```Bash
# View allocatable resources (what's available for pods)
kubectl describe node NODE_NAME | grep -A5 "Allocatable"

# View resource usage
kubectl top node
```

## Node Info (OS, Kernel, Runtime)

```Bash
kubectl get node NODE_NAME -o jsonpath='{.status.nodeInfo}'
```

## Node Taints Applied by System

```Bash
# View all taints on all nodes
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
```

Common system [[Taints]] on control plane nodes:
`node-role.kubernetes.io/control-plane:NoSchedule`

## Node Capacity vs Allocatable

```Bash
kubectl describe node NODE_NAME | grep -A6 "Capacity:"
kubectl describe node NODE_NAME | grep -A6 "Allocatable:"
```

`Allocatable` = `Capacity` minus kubelet-reserved and system-reserved resources.

## Node Conditions

```Bash
kubectl get node NODE_NAME -o jsonpath='{.status.conditions[*].type}'
