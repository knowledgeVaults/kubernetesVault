**Rollbacks** revert a [[Deployments]] to a previous revision by scaling up a retained [[ReplicaSets]] and scaling down the current one

* Kubernetes retains old ReplicaSets (scaled to 0) as rollback targets as the number kept is controlled by `revisionHistoryLimit` (default 10)
* A rollback is itself a new rollout as it follows the same rolling update strategy
* The rollback target can be a specific revision number or simply the previous revision

## Triggering a Rollback

```Bash
# Roll back to the immediately previous revision
kubectl rollout undo deployment/my-deployment -n NAMESPACE

# Roll back to a specific revision number
kubectl rollout undo deployment/my-deployment --to-revision=3 -n NAMESPACE
```

## Viewing Rollout History

```Bash
# List all revisions
kubectl rollout history deployment/my-deployment

# Inspect a specific revision
kubectl rollout history deployment/my-deployment --revision=2
# Shows: image, labels, annotations for that revision
```

## Annotating Deployments for Better History

```Bash
# Add a change-cause annotation so history is readable
kubectl annotate deployment/my-deployment kubernetes.io/change-cause="Bumped nginx to 1.25.3"
```

Or set it in the manifest:
```YAML
metadata:
  annotations:
    kubernetes.io/change-cause: "Upgraded to v2.3.1 — security patch for CVE-2024-1234"
```

## Verifying a Rollback

```Bash
kubectl rollout status deployment/my-deployment   # Wait for completion
kubectl get deployment my-deployment -o jsonpath='{.spec.template.spec.containers[0].image}'
kubectl describe deployment my-deployment | grep Image
```

## Controlling Revision History

```YAML
spec:
  revisionHistoryLimit: 5    # Keep only the last 5 ReplicaSets (save etcd space)
```

Setting to `0` disables rollback history so this is not recommended for production.

## Rollback vs Redeploy

A rollback scales up an existing old ReplicaSet as it is faster than a full redeploy because the pods use images already cached on nodes. Use rollback for emergency reversions and a proper redeployment for long-term fixes.
