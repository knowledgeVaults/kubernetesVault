The **[[kube-scheduler]]** processes pod placement through a structured pipeline of phases, each implemented as a set of configurable plugins

## Scheduling Pipeline

```
PreEnqueue → Queue Sort → PreFilter → Filter → PostFilter → PreScore → Score → Reserve → Permit → PreBind → Bind → PostBind
```

### PreEnqueue

Runs before a pod is added to the scheduling queue

* Plugins can block a pod from entering the queue (e.g., a gate not yet satisfied)
* Introduced in Kubernetes 1.27 via the `schedulingGates` feature

### Queue Sort

Determines the order in which pending pods are popped from the scheduling queue

* Default plugin: `PrioritySort` which orders pods by `spec.priority`
* Higher priority pods are scheduled before lower priority pods

### PreFilter

Validates and preprocesses information for the filter phase; can reject a pod early

* Checks that prerequisites are satisfied before expensive per-node filtering begins
* Example: `InterPodAffinity` checks that required anti-affinity terms can possibly be satisfied

### Filter

Eliminates nodes that cannot run the pod (**predicates**)

* `NodeResourcesFit`: node must have enough allocatable CPU and memory
* `NodeAffinity`: node labels must match `nodeAffinity` rules
* `TaintToleration`: pod tolerations must cover all required node taints
* `VolumeBinding`: required PVCs must be schedulable on the node

### PostFilter (Preemption)

Called only when **no nodes passed the Filter phase**

* Default plugin: `DefaultPreemption` which attempts to evict lower-priority pods to make room
* If preemption is possible, the pod is placed in a nominatedNode queue and re-evaluated

### Score

Ranks all nodes that passed filtering; each plugin scores a node 0–100

* `NodeResourcesBalancedAllocation`: prefers nodes with balanced CPU/memory ratios
* `ImageLocality`: prefers nodes that already have the required image cached
* Final score = weighted sum of all plugin scores

### Reserve / Bind

`Reserve` performs pessimistic resource reservation; `Bind` writes the scheduling decision

```YAML
# Binding written to the API server
apiVersion: v1
kind: Binding
metadata:
  name: MY_POD
target:
  apiVersion: v1
  kind: Node
  name: SELECTED_NODE
```
