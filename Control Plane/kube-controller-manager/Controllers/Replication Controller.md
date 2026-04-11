The **Replication Controller** is the original pod replication mechanism in Kubernetes that ensures a specified number of pod replicas are running at all times

* **Deprecated** and is replaced by [[ReplicaSets]] which supports set-based selectors
* Functionally equivalent to a ReplicaSet with equality-based selectors only
* Existing clusters may still have ReplicationControllers from legacy configurations

## How It Works

The Replication Controller continuously reconciles actual pod count against `spec.replicas`

* If actual < desired: creates new pods using the pod template
* If actual > desired: deletes excess pods
* If a pod crashes: the [[kubelet]] reports it as `Failed`; the controller creates a replacement

## ReplicationController Manifest

```YAML
apiVersion: v1
kind: ReplicationController
metadata:
  name: my-rc
  namespace: default
spec:
  replicas: 3
  selector:
    app: my-app      # Equality-based selector only (key=value)
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: app
          image: my-app:v1
          ports:
            - containerPort: 8080
```

## ReplicationController vs ReplicaSet

| Feature | ReplicationController | ReplicaSet |
|---|---|---|
| Selector type | Equality only (`key=value`) | Equality + set-based (`In`, `NotIn`, `Exists`) |
| Used by Deployments | No | Yes |
| Recommended | No (deprecated) | Yes |
| API version | `v1` | `apps/v1` |

## Migrating from RC to ReplicaSet

1. Create an equivalent ReplicaSet with the same selector and template
2. Scale the RC to 0: `kubectl scale rc my-rc --replicas=0`
3. Delete the RC: `kubectl delete rc my-rc`
4. The RS adopts existing pods if their labels match its selector

## Commands

```Bash
kubectl get rc [-n NAMESPACE]
kubectl scale rc my-rc --replicas=5
kubectl delete rc my-rc
```

## Identifying ReplicationControllers
```Bash
kubectl get rc -A
kubectl get rc -A -o jsonpath='{range .items[*]}{.metadata.namespace}/{.metadata.name}{"
"}{end}'
```
If any ReplicationControllers exist in your cluster, migrate them to Deployments or ReplicaSets for full feature support.
