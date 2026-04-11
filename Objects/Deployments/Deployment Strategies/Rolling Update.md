The **Rolling Update** [[Deployments]] strategy gradually replaces old pod replicas with new ones, ensuring the application remains available throughout the transition

* Default strategy in Kubernetes since no configuration required to use it
* Controls the rollout pace via `maxUnavailable` and `maxSurge`
* Old and new pod versions run simultaneously during the transition window

## Configuration

```YAML
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1    # How many pods can be unavailable during rollout
      maxSurge: 1          # How many extra pods can be created above replicas
```

Both values accept absolute numbers or percentages (`"25%"`).

## maxUnavailable

Controls the minimum availability during the rollout

* `maxUnavailable: 0` specifies no pods are ever removed before the replacement is `Ready`; zero-downtime guaranteed
* `maxUnavailable: 1` specifies one pod can be taken down before the replacement is ready (faster, slight risk)
* Default: `25%` of the desired replica count

## maxSurge

Controls how many extra pods beyond the desired count can exist during the rollout

* `maxSurge: 0` specifies no extra pods; old pods must be removed before new ones are created (not recommended with `maxUnavailable: 0`)
* `maxSurge: 1` specifies one extra pod is created first, then old pods are replaced
* Default: `25%` of the desired replica count

## Rollout Lifecycle

```
Desired: 3 replicas, maxUnavailable=1, maxSurge=1

Step 1: Create 1 new pod (total: 4 — 3 old + 1 new)
Step 2: Wait for new pod to be Ready
Step 3: Remove 1 old pod (total: 3 — 2 old + 1 new)
Step 4: Repeat until all pods are new
```

## Monitoring a Rolling Update

```Bash
kubectl rollout status deployment/my-deployment
kubectl get pods -w                     # Watch pods being replaced
kubectl rollout history deployment/my-deployment
kubectl rollout undo deployment/my-deployment   # Rollback if needed
```

## Zero-Downtime Configuration

```YAML
rollingUpdate:
  maxUnavailable: 0
  maxSurge: 1
```

Combined with a properly configured `readinessProbe`, this guarantees zero-downtime deployments.
