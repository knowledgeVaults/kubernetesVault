A **Binding** is a Kubernetes API object that explicitly assigns a Pod to a specific Node, bypassing the [[kube-scheduler]]

* Normally created internally by the scheduler after it selects a node for a pending pod
* Can be created manually to bind a pending pod to a specific node without a running scheduler
* Once a pod is bound, the [[kubelet]] on the target node picks it up and starts the containers

## Binding Definition

```YAML
apiVersion: v1
kind: Binding
metadata:
  name: POD_NAME
  namespace: NAMESPACE
target:
  apiVersion: v1
  kind: Node
  name: NODE_NAME
```

## Creating a Binding Manually

The Binding object is submitted as a POST to the pod's binding subresource via the API

```Bash
# Using kubectl (requires encoding the body as JSON)
kubectl create -f binding.yaml

# Or via curl directly
curl -X POST \
  https://APISERVER:6443/api/v1/namespaces/default/pods/MY_POD/binding \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer TOKEN" \
  --cacert /path/to/ca.crt \
  -d '{
    "apiVersion": "v1",
    "kind": "Binding",
    "metadata": {"name": "MY_POD"},
    "target": {"apiVersion": "v1", "kind": "Node", "name": "NODE_NAME"}
  }'
```

## Relationship to Manual Scheduling

Setting `spec.nodeName` on a Pod manifest at creation time achieves the same result without creating a Binding object

```YAML
spec:
  nodeName: worker-node-1   # Pod is directly assigned; scheduler is bypassed entirely
```

* `spec.nodeName` is set at creation — the pod never enters the scheduler queue
* `Binding` is used to assign a pod that is already `Pending` (already in the scheduler queue)

## Use Cases

* Bootstrapping before the scheduler is available (Static Pods do not use Bindings)
* Testing or debugging specific node-pod combinations
* Custom scheduler implementations that write Binding objects as their final step

## Verifying a Binding

```Bash
kubectl get pod POD_NAME -o jsonpath='{.spec.nodeName}'
kubectl describe pod POD_NAME | grep "Node:"
# Node: worker-node-1/10.0.0.5
```

If the pod remains `Pending` after a manual Binding, check node conditions, taints, and resource availability.
