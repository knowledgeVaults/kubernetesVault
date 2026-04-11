The [[kube-scheduler]] is the control plane component responsible for assigning unscheduled Pods to suitable nodes

* Watches the [[kube-apiserver]] for Pods with no `spec.nodeName` set (pending pods)
* Selects the best node using a two-phase process: **filtering** (removes ineligible nodes) then **scoring** (ranks eligible nodes)
* Writes the scheduling decision back to the API server, which creates a `Binding` object to bind the Pod to the node

## Scheduling Pipeline

```
Pending Pod → PreFilter → Filter → PostFilter → PreScore → Score → Reserve → Bind
```

### Filter Phase (Predicates)

Eliminates nodes that cannot run the Pod

* `NodeResourcesFit`: node must have enough CPU and memory for the Pod's requests
* `NodeAffinity`: node must satisfy `nodeAffinity` rules
* `TaintToleration`: node taints must be tolerated by the Pod
* `VolumeZone`: PVCs must be accessible from the candidate node's zone

### Score Phase (Priorities)

Ranks remaining nodes to select the best fit

* `NodeResourcesBalancedAllocation`: prefers nodes with balanced CPU/memory usage
* `ImageLocality`: prefers nodes that already have the container image cached
* `InterPodAffinity`: prefers nodes that satisfy pod affinity/anti-affinity

## Static Pod Manifest

In [[kubeadm]] clusters, the scheduler runs as a Static Pod

```Bash
cat /etc/kubernetes/manifests/kube-scheduler.yaml
```

## Key Flags

| Flag | Description |
|---|---|
| `--config` | Path to KubeSchedulerConfiguration file |
| `--leader-elect` | Enable HA [[Architecture/Leader Election]] (default true) |
| `--bind-address` | Address to serve health/metrics endpoints |

## Checking Scheduler Status

```Bash
kubectl get pod -n kube-system -l component=kube-scheduler
kubectl logs -n kube-system kube-scheduler-NODENAME
kubectl get events -n NAMESPACE --field-selector reason=FailedScheduling
```

## Scheduling Failure Events

```Bash
kubectl get events -n NAMESPACE --field-selector reason=FailedScheduling
# Example message: 0/3 nodes are available: 3 Insufficient memory
```

Common reasons: insufficient CPU/memory, no nodes satisfy nodeAffinity, all nodes are tainted without matching tolerations.
