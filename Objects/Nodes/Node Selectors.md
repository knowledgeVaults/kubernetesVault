
 [[Node Selectors]] constrain pod scheduling to nodes that have specific labels, using simple equality-based matching

* Requires that a label be applied to the node beforehand

```YAML
apiVersion:
kind:
metadata:
spec:
  nodeSelector:
    KEY: VALUE 
```

* The simplest way to target pods to specific nodes
* For more complex rules (In, NotIn, Exists), use `nodeAffinity` instead

## Applying a nodeSelector

Example: Force the pod to run only on nodes that have both the *hardware=gpu* and *kubernetes.io/arch=amd64* labels
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  nodeSelector:
    hardware: gpu
    kubernetes.io/arch: amd64
  containers:
    - name: gpu-app
      image: nvidia/cuda:latest
```

## Prerequisites

The target label must exist on the node before scheduling

```Bash
# Label a node
kubectl label node NODE_NAME hardware=gpu

# Verify the label
kubectl get node NODE_NAME --show-labels
kubectl get nodes -l hardware=gpu
```

## nodeSelector vs nodeAffinity

| Feature | nodeSelector | nodeAffinity |
|---|---|---|
| Operators | Equality only | In, NotIn, Exists, DoesNotExist, Gt, Lt |
| Required vs Preferred | Required only | Both required and preferred |
| Expressiveness | Simple | Full |

For most production use cases, prefer `nodeAffinity` over `nodeSelector`.

## Built-in Node Labels

Kubernetes automatically applies these labels to nodes:

| Label | Example Value |
|---|---|
| `kubernetes.io/hostname` | `worker-node-1` |
| `kubernetes.io/arch` | `amd64` |
| `kubernetes.io/os` | `linux` |
| `node.kubernetes.io/instance-type` | `m5.xlarge` |
| `topology.kubernetes.io/region` | `us-east-1` |
| `topology.kubernetes.io/zone` | `us-east-1a` |


## Removing a Node Label
```Bash
kubectl label node NODE_NAME hardware-
# Pods with nodeSelector hardware=gpu will become unschedulable if no other matching node exists
```

## Checking Pending Pods

If a pod is stuck in `Pending` with `nodeSelector` defined:
```Bash
kubectl describe pod POD_NAME | grep -A5 "Events"
# "0/3 nodes are available: 3 node(s) didn't match node selector"
```

## Using nodeAffinity Instead

```YAML
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: hardware
              operator: In
              values: [gpu, tpu]
```
This is equivalent to `nodeSelector` but supports `In`, `NotIn`, and other set operators.
