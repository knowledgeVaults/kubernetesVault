A **[[ReplicaSets]]** ensures a specified number of Pod replicas matching its selector are running at all times

* Provides self-healing: automatically recreates Pods when they crash or are deleted
* Rarely used directly and is managed by [[Deployments]] objects which add rollout and rollback capabilities
* Uses `spec.selector` to identify Pods it owns; any Pod matching the selector is claimed, even if created manually

## ReplicaSet Manifest

```YAML
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-rs
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
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

## Reconciliation Behaviour

The ReplicaSet Controller continuously reconciles actual vs desired replica count

* **Too few pods**: controller creates new Pods using the pod template
* **Too many pods**: controller deletes excess Pods (newest first by default)
* **Pod crashes**: [[kubelet]] reports the pod as `Failed`; controller creates a replacement

## Scaling

```Bash
kubectl scale replicaset my-rs --replicas=5
kubectl patch replicaset my-rs -p '{"spec":{"replicas":2}}'
```

## Pod Template Hash

When a Deployment creates a ReplicaSet, it adds a `pod-template-hash` label to the ReplicaSet and all its Pods

* Ensures the Deployment can distinguish between ReplicaSets from different rollouts
* Do not manually set or modify this label

## ReplicaSet vs ReplicationController

| Feature | ReplicaSet | ReplicationController |
|---|---|---|
| Selector type | `matchLabels` + `matchExpressions` | Equality-based only |
| Used by Deployments | Yes | No |
| Recommended | Yes | No (deprecated) |

## Commands

```Bash
kubectl get replicaset [-n NAMESPACE]
kubectl describe replicaset RS_NAME
kubectl delete replicaset RS_NAME    # Deletes RS and its pods
kubectl delete replicaset RS_NAME --cascade=orphan  # Deletes RS only; pods remain
```
