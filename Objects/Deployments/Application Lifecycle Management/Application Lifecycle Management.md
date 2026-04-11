**Application Lifecycle Management (ALM)** in Kubernetes refers to the practices and mechanisms for managing the full lifecycle of application workloads (From initial [[Deployments]] through updates and rollbacks)

* Kubernetes provides built-in ALM primitives via `Deployment` objects and their rollout controls
* ALM covers: initial deployment, rolling updates, canary releases, rollbacks, and version tracking

## Key Concepts

### Rollouts

A rollout is the process of transitioning a Deployment from one Pod template version to another

* Triggered automatically whenever `spec.template` changes (e.g., a new image tag)
* Controlled by the `strategy` field with the value being either `RollingUpdate` (default) or `Recreate`
* Progress is tracked via `.status.conditions` and `kubectl rollout status`

### Revisions

Each rollout creates a new [[ReplicaSets]] and increments the Deployment revision counter

* Kubernetes retains old ReplicaSets (scaled to 0) as revision history for rollback
* The number of retained revisions is controlled by `revisionHistoryLimit` (default 10)
* Revision history is viewable via `kubectl rollout history deployment/NAME`

### Rollbacks

Rolling back restores the Deployment to a previous revision by scaling up the corresponding old ReplicaSet

```Bash
kubectl rollout undo deployment/NAME
kubectl rollout undo deployment/NAME --to-revision=3
```

## Lifecycle Commands

```Bash
# Trigger a rollout by updating the image
kubectl set image deployment/NAME CONTAINER=IMAGE:TAG

# Watch rollout progress
kubectl rollout status deployment/NAME

# Pause a rollout (for canary inspection)
kubectl rollout pause deployment/NAME

# Resume a paused rollout
kubectl rollout resume deployment/NAME

# View rollout history
kubectl rollout history deployment/NAME

# Rollback to previous version
kubectl rollout undo deployment/NAME
```

## Deployment Strategies

| Strategy | Behavior | Downtime |
|---|---|---|
| `RollingUpdate` | Gradually replaces old pods with new ones | None |
| `Recreate` | Terminates all old pods before creating new ones | Brief |
