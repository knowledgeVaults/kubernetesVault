**[[Deployments]] Revisions** are numbered snapshots of a Deployment's pod template, created each time the template changes as they serve as rollback targets and provide an audit trail of changes

* Each revision corresponds to a retained [[ReplicaSets]] (scaled to 0) in the cluster
* The number of revisions retained is controlled by `spec.revisionHistoryLimit` (default 10)
* Revision numbers increment monotonically and are stored in the ReplicaSet's `deployment.kubernetes.io/revision` annotation

## How Revisions Are Created

A new revision is created when `spec.template` changes

```Bash
# Each of these triggers a new revision:
kubectl set image deployment/my-app app=my-app:v2         # Image change
kubectl set env deployment/my-app APP_ENV=staging          # Env var change
kubectl edit deployment/my-app                             # Manual spec change
kubectl apply -f deployment.yaml                           # Manifest update
```

Scaling changes (`spec.replicas`) do **not** create new revisions.

## Viewing Revision History

```Bash
kubectl rollout history deployment/my-deployment
# REVISION  CHANGE-CAUSE
# 1         Initial release
# 2         nginx 1.24 → 1.25
# 3         Added memory limit

# Inspect a specific revision
kubectl rollout history deployment/my-deployment --revision=2
# Shows: pod template spec for that revision
```

## Adding Change Cause Annotations

```Bash
kubectl annotate deployment/my-deployment \
  kubernetes.io/change-cause="Upgraded to v2.3.1 for security patch"
```

Or in the manifest:
```YAML
metadata:
  annotations:
    kubernetes.io/change-cause: "CVE-2024-1234 fix"
```

## Rolling Back to a Revision

```Bash
kubectl rollout undo deployment/my-deployment                  # Previous revision
kubectl rollout undo deployment/my-deployment --to-revision=2  # Specific revision
```

## Controlling History Size

```YAML
spec:
  revisionHistoryLimit: 5    # Retain only the last 5 old ReplicaSets
```

Lower values save [[etcd]] storage; higher values allow rolling back further.
