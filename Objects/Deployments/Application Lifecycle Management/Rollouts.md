**Rollouts** are the process by which a [[Deployments]] transitions from one pod template to another, replacing old pods with new ones according to the configured deployment strategy

* Triggered automatically whenever `spec.template` changes  and is used most commonly when the container image is updated
* Tracked via the Deployment's `status.conditions` and observable with `kubectl rollout status`
* Each rollout creates a new `ReplicaSets` and increments the revision counter

## Triggering a Rollout

```Bash
# Update the container image (most common trigger)
kubectl set image deployment/my-deployment app=my-app:v2 -n NAMESPACE

# Apply an updated manifest
kubectl apply -f deployment.yaml

# Force a rollout without changing the manifest (e.g., to pick up a new ConfigMap)
kubectl rollout restart deployment/my-deployment
```

## Checking Rollout Status

```Bash
# Wait for completion (blocks until done or fails)
kubectl rollout status deployment/my-deployment -n NAMESPACE

# Watch pods being replaced in real-time
kubectl get pods -l app=my-app -w
```

## Pausing and Resuming

Pause a rollout to inspect the new version before full deployment (canary-style)

```Bash
kubectl rollout pause deployment/my-deployment

# Optionally update more settings while paused
kubectl set resources deployment/my-deployment -c app --limits=cpu=500m

# Resume to continue the rollout
kubectl rollout resume deployment/my-deployment
```

## Rollout History

A new revision is created for each rollout (The previous ReplicaSets are retained for rollback)

```Bash
kubectl rollout history deployment/my-deployment
# REVISION  CHANGE-CAUSE
# 1         Initial deployment
# 2         Bumped nginx to 1.25.3
# 3         Added health check endpoint
```

## Rollout Completion Indicators

A rollout is complete when `status.updatedReplicas == status.replicas == status.availableReplicas` and `status.unavailableReplicas == 0`.

```Bash
kubectl get deployment my-deployment
# READY column: 3/3 = rollout complete
```

## Rollout Failure Detection

If the rollout does not complete within `spec.progressDeadlineSeconds` (default 600s), Kubernetes marks the Deployment as failed:
```Bash
kubectl describe deployment my-deployment | grep "Progressing"
# Condition: Progressing=False, reason=ProgressDeadlineExceeded
```
