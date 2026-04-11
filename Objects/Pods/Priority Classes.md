
Priority classes define the importance of Pods and allow the scheduler to decide which Pods should run first and ones should be evicted first when the cluster lacks resources

* Defined using a `PriorityClass` object
* The scheduler will always prioritize higher priority workloads (Kubernetes components always get the higher priority)

Create a priority class
```YAML
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: PRIORITY_CLASS_NAME
value: NUMBER
globalDefault: {true|false}
description: "DESCRIPTION"
preemptionPolicy: 
```

Have a Pod use a priority class
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: POD_NAME
spec:
  priorityClassName: PRIORITY_CLASS_NAME
  containers:
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
```

List all the priority classes
```Bash
kubectl get priorityclass
```

Describe a priority class
```Bash
kubectl describe priorityclass PRIORITY_CLASS
```

## Default Priority Classes

Kubernetes ships with two built-in priority classes for system components

```Bash
kubectl get priorityclass
# system-cluster-critical    2000000000    true
# system-node-critical       2000001000    true
```

These ensure core components ([[CoreDNS]], [[kube-proxy]], etc.) are never evicted before user workloads.

## Priority and Preemption

When a high-priority pod cannot be scheduled due to resource constraints, the scheduler may **preempt** (evict) lower-priority pods to make room

```YAML
preemptionPolicy: PreemptLowerPriority    # Default: evict lower-priority pods
# or
preemptionPolicy: Never                   # Non-preempting: wait in queue
```

## Eviction Order Under Pressure

When a node is under memory pressure, [[kubelet]] evicts pods in this order:
1. `BestEffort` pods (no requests/limits)
2. `Burstable` pods with usage above their requests
3. `Guaranteed` pods (requests == limits) which are the last to be evicted

## Anti-Patterns

Do not give high priority values to application workloads just to prevent eviction. Instead, set proper resource requests and limits, define PodDisruptionBudgets, and use `Guaranteed` QoS class. Overusing priority classes degrades cluster stability.

```Bash
kubectl describe priorityclass high-priority    # View details
kubectl get pods --sort-by='.spec.priority'     # Sort pods by priority
```
