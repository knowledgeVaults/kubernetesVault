**Manual Scheduling** is the process of assigning a Pod to a specific node directly, bypassing the [[kube-scheduler]] entirely

* Done by setting the `spec.nodeName` field in the Pod manifest before the Pod is created
* The [[kubelet]] on the target node picks up the Pod and runs it without any scheduler involvement
* Useful for testing, debugging, or running Pods on specific nodes when the scheduler is unavailable

## Setting nodeName at Creation Time

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  nodeName: worker-node-1
  containers:
    - name: nginx
      image: nginx:latest
```

* You **cannot** modify `spec.nodeName` on a running Pod as it is immutable after creation
* If the specified node does not exist or is `NotReady`, the Pod stays in `Pending` state

## Binding an Already-Pending Pod

If a Pod is already pending (no scheduler available), you can bind it manually via the Binding API

```YAML
apiVersion: v1
kind: Binding
metadata:
  name: my-pod
target:
  apiVersion: v1
  kind: Node
  name: worker-node-1
```

```Bash
curl -X POST \
  http://APISERVER:8080/api/v1/namespaces/default/pods/my-pod/binding \
  -H "Content-Type: application/json" \
  -d '{"apiVersion":"v1","kind":"Binding","metadata":{"name":"my-pod"},"target":{"apiVersion":"v1","kind":"Node","name":"worker-node-1"}}'
```

## Use Cases

* Bootstrapping control plane Static Pods before the scheduler is available
* Pinning a diagnostic or monitoring pod to a specific node for targeted inspection
* Running jobs that require access to hardware resources only present on a specific node (e.g., GPU, custom device)

## Limitations

* Ignores resource constraints since the kubelet will still enforce limits but the scheduler's fit checks are skipped
* Ignores taints and node conditions since the Pod lands even if the node is cordoned or tainted
* Not suitable for production workloads; use `nodeSelector` or `nodeAffinity` instead for static node targeting

## Verifying Manual Placement

```Bash
kubectl get pod my-pod -o wide
# NODE column shows the assigned node

kubectl describe pod my-pod | grep Node:
# Node: worker-node-1/10.0.0.5
```
