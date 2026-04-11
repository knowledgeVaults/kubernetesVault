A **[[Deployments]]** is the standard way to run and manage stateless applications in Kubernetes, providing declarative updates, rollouts, and rollbacks via ReplicaSets

* Manages [[ReplicaSets]] objects as intermediaries — Pods are created by the ReplicaSet, not directly by the Deployment
* Triggers a new rollout whenever `spec.template` changes (e.g., image update, env var change)
* Retains old ReplicaSets (scaled to 0) for rollback purposes, up to `revisionHistoryLimit` (default 10)

## Deployment Manifest

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  namespace: default
spec:
  replicas: 3
  revisionHistoryLimit: 5
  selector:
    matchLabels:
      app: my-app
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: app
          image: my-app:v2
          ports:
            - containerPort: 8080
          resources:
            requests:
              cpu: "100m"
              memory: "128Mi"
```

## Rollout Commands

```Bash
# Update the image
kubectl set image deployment/my-deployment app=my-app:v3

# Check rollout status
kubectl rollout status deployment/my-deployment

# View history
kubectl rollout history deployment/my-deployment

# Roll back
kubectl rollout undo deployment/my-deployment
kubectl rollout undo deployment/my-deployment --to-revision=2

# Pause / resume
kubectl rollout pause deployment/my-deployment
kubectl rollout resume deployment/my-deployment
```

## Deployment Status Conditions

| Condition | Meaning |
|---|---|
| `Available` | Minimum desired pods are ready |
| `Progressing` | Rollout is underway |
| `ReplicaFailure` | ReplicaSet could not create pods |

## Editing a Deployment

```Bash
kubectl edit deployment my-deployment
kubectl apply -f deployment.yaml
```

## Deployment vs [[Stateful Sets]] vs [[Daemon Sets]]

| Controller | Use Case |
|---|---|
| `Deployment` | Stateless apps (web servers, APIs); pods are interchangeable |
| `StatefulSet` | Stateful apps (databases); pods have stable identity and storage |
| `DaemonSet` | System agents; exactly one pod per (matching) node |
