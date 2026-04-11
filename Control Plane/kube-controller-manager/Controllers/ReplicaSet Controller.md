The **[[ReplicaSets]] Controller** ensures that the correct number of Pod replicas matching a selector are running at all times

* Continuously reconciles the desired replica count (`spec.replicas`) against the actual number of matching Pods
* Creates Pods when actual count < desired; deletes excess Pods when actual count > desired
* Rarely managed directly as it's typically owned and controlled by the [[Deployments]] Controller

## Selector Matching

The ReplicaSet Controller uses the `spec.selector` to identify Pods it owns

```YAML
spec:
  selector:
    matchLabels:
      app: my-app
      version: v2
```

* Any Pod matching this selector is considered part of the ReplicaSet including mannually created Pods
* If extra Pods matching the selector exist, the controller will delete them to reach the desired count
* Always ensure Pod template labels match the selector exactly to avoid unintended adoption

## Ownership via ownerReferences

Pods created by a ReplicaSet carry an `ownerReference` pointing back to the ReplicaSet

* If the ReplicaSet is deleted with `--cascade=foreground`, owned Pods are garbage-collected
* If deleted with `--cascade=orphan`, Pods are left running but lose their owner

## ReplicaSet vs ReplicationController

| Feature | ReplicaSet | ReplicationController |
|---|---|---|
| Selector type | `matchLabels` and `matchExpressions` | Equality-based only |
| Recommended | Yes | No (deprecated) |
| Used by Deployments | Yes | No |

## Scaling

```Bash
kubectl scale replicaset MY_RS --replicas=5
kubectl patch replicaset MY_RS -p '{"spec":{"replicas":3}}'
```

The controller responds to the changed `spec.replicas` within milliseconds via its watch stream.

## Checking ReplicaSet Status

```Bash
kubectl get replicaset -n NAMESPACE
kubectl describe replicaset MY_RS
# DESIRED / CURRENT / READY columns show reconciliation status

kubectl get events --field-selector involvedObject.kind=ReplicaSet
```

The controller emits events for pod creation failures, allowing easy diagnosis of scheduling issues.
