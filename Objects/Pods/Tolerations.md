**Tolerations** are properties defined on a Pod that allow it to be scheduled on nodes with matching **Taints**

* Taints repel pods; tolerations allow specific pods to override that repulsion
* A [[Tolerations]] does not force a pod onto a tainted node because it only permits scheduling there
* Combine tolerations with `nodeAffinity` or `nodeSelector` to both permit and target specific nodes

## Toleration Structure

```YAML
spec:
  tolerations:
    - key: "node-role"
      operator: "Equal"
      value: "gpu"
      effect: "NoSchedule"
    - key: "maintenance"
      operator: "Exists"
      effect: "NoExecute"
      tolerationSeconds: 3600
```

## Operators

| Operator | Meaning |
|---|---|
| `Equal` | Toleration matches taints with the same key and value |
| `Exists` | Toleration matches all taints with the given key, regardless of value |

## [[Taints]] Effects and What the Toleration Allows

| Effect | Without Toleration | With Matching Toleration |
|---|---|---|
| `NoSchedule` | Pod will not be scheduled on the node | Pod can be scheduled |
| `PreferNoSchedule` | Scheduler avoids the node but may still place pods | Pod can be scheduled (already soft) |
| `NoExecute` | Running pods are evicted; new pods not scheduled | Pod can run; eviction is optional |

## tolerationSeconds

`tolerationSeconds` applies only to `NoExecute` tolerations and specifies how long an already-running pod can remain on the node after the taint is added

```YAML
- key: "node.kubernetes.io/not-ready"
  operator: "Exists"
  effect: "NoExecute"
  tolerationSeconds: 300
```

* After 300 seconds, the pod is evicted if the node remains `NotReady`
* Without `tolerationSeconds`, the pod runs indefinitely on the tainted node

## Built-in Tolerations Added by Kubernetes

| Taint | Added by | Purpose |
|---|---|---|
| `node.kubernetes.io/not-ready:NoExecute` | Node Controller | Grace period before eviction when node is not ready |
| `node.kubernetes.io/unreachable:NoExecute` | Node Controller | Grace period before eviction when node is unreachable |
