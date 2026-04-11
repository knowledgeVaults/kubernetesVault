**Taints and Tolerations** work together to control which pods can be scheduled on which nodes

* A **[[Taints]]** on a node repels pods that do not have a matching [[Tolerations]]
* A **Toleration** on a pod permits it to be scheduled on a tainted node
* Neither taints nor tolerations guarantee pod placement —(Use `nodeAffinity` or `nodeSelector` to actively target specific nodes)

## Taints

### Adding a Taint

```Bash
kubectl taint node NODE_NAME KEY=VALUE:EFFECT
```

### Taint Effects

| Effect | Description |
|---|---|
| `NoSchedule` | New pods without a matching toleration will NOT be scheduled |
| `PreferNoSchedule` | Scheduler avoids the node but may still place pods there |
| `NoExecute` | Running pods without a toleration are evicted; new pods blocked |

### Removing a Taint

```Bash
kubectl taint node NODE_NAME KEY=VALUE:EFFECT-
```

## Tolerations

### Toleration on a Pod

```YAML
apiVersion: v1
kind: Pod
spec:
  tolerations:
    - key: "dedicated"
      operator: "Equal"
      value: "gpu"
      effect: "NoSchedule"
    - key: "node.kubernetes.io/not-ready"
      operator: "Exists"
      effect: "NoExecute"
      tolerationSeconds: 300
```

### Operators

| Operator | Matches |
|---|---|
| `Equal` | Same key and value |
| `Exists` | Same key; any value |

## Use Cases

| Scenario | Approach |
|---|---|
| Dedicate nodes to a team | Taint nodes with team label; team pods tolerate it |
| Mark a node for maintenance | Add `NoSchedule` taint; drain existing pods |
| GPU nodes | Taint with `hardware=gpu:NoSchedule`; ML pods tolerate it |
| Control plane isolation | `node-role.kubernetes.io/control-plane:NoSchedule` prevents user pods |

## Viewing Taints on Nodes

```Bash
kubectl describe nodes | grep Taints
kubectl get node NODE_NAME -o jsonpath='{.spec.taints}'
```

## Built-in Kubernetes Taints

Kubernetes automatically taints nodes under certain conditions

| Taint | Condition |
|---|---|
| `node.kubernetes.io/not-ready:NoExecute` | Node is NotReady |
| `node.kubernetes.io/unreachable:NoExecute` | Node is unreachable |
| `node.kubernetes.io/memory-pressure:NoSchedule` | MemoryPressure |
| `node.kubernetes.io/disk-pressure:NoSchedule` | DiskPressure |
| `node.kubernetes.io/unschedulable:NoSchedule` | Cordoned |
| `node-role.kubernetes.io/control-plane:NoSchedule` | Control plane node |

## Default Tolerations

All pods get two default tolerations automatically (added by the `DefaultTolerationSeconds` [[Admission Controllers]]):

```YAML
