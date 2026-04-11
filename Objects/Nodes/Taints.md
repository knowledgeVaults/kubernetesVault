**Taints** are key-value-effect properties applied to nodes that repel pods from being scheduled on them unless the pod has a matching **[[Tolerations]]**

* Taints are the "push" mechanism; Tolerations are the "pull" mechanism
* Used to dedicate nodes to specific workloads, mark nodes for maintenance, or exclude general workloads from GPU/specialty hardware nodes

## [[Taints]] Structure

```
key=value:effect
```

| Part | Required | Description |
|---|---|---|
| `key` | Yes | Label key (e.g., `dedicated`, `node.kubernetes.io/not-ready`) |
| `value` | No | Optional value matching a toleration |
| `effect` | Yes | One of: `NoSchedule`, `PreferNoSchedule`, `NoExecute` |

## Taint Effects

| Effect             | Behaviour on Pods Without Toleration                     |
| ------------------ | -------------------------------------------------------- |
| `NoSchedule`       | New pods will not be scheduled on the node               |
| `PreferNoSchedule` | Scheduler tries to avoid this node; not a hard guarantee |
| `NoExecute`        | Running pods are evicted; new pods not scheduled         |

## Taint Commands

```Bash
# Add a taint
kubectl taint node NODE_NAME key=value:NoSchedule
kubectl taint node NODE_NAME dedicated=gpu:NoSchedule

# Remove a taint (suffix with -)
kubectl taint node NODE_NAME key=value:NoSchedule-
kubectl taint node NODE_NAME dedicated=gpu:NoSchedule-

# View all taints on a node
kubectl describe node NODE_NAME | grep Taints
```

## System Taints (Added Automatically)

| Taint | Added When |
|---|---|
| `node.kubernetes.io/not-ready:NoExecute` | Node enters `NotReady` state |
| `node.kubernetes.io/unreachable:NoExecute` | Node becomes unreachable |
| `node.kubernetes.io/memory-pressure:NoSchedule` | Node has `MemoryPressure` |
| `node.kubernetes.io/disk-pressure:NoSchedule` | Node has `DiskPressure` |
| `node.kubernetes.io/unschedulable:NoSchedule` | Node is cordoned |
| `node-role.kubernetes.io/control-plane:NoSchedule` | Marks control plane nodes |

## Typical Use Case: Dedicated Nodes

```Bash
# Taint the GPU node
kubectl taint node gpu-node-1 hardware=gpu:NoSchedule

# Pods wanting the GPU must add a toleration
tolerations:
  - key: "hardware"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```
