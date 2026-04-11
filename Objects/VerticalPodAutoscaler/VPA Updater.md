
* Detects Pods running with suboptimal resources and evicts them when an update is needed
* Gets the information from the Recommender and evicts Pods when they need to be updated

## How the VPA Updater Works

The VPA Updater runs as a [[Deployments]] in the cluster and operates on a periodic loop

1. Fetches current VPA objects and their recommendations from the Recommender
2. Identifies pods whose current resource requests are significantly outside the recommended range
3. Evicts the out-of-range pods so they are recreated by their controller (Deployment, [[Stateful Sets]])
4. The VPA [[Admission Controllers]] intercepts pod creation and applies the updated resource requests

## Update Modes

The Updater only evicts pods when the VPA `updatePolicy.updateMode` permits it

| Mode | Updater behaviour |
|---|---|
| `Auto` | Updater evicts pods when resources need adjustment |
| `Recreate` | Same as Auto but only during pod recreation |
| `Initial` | Resources set at pod creation only; Updater does not evict |
| `Off` | Recommendations generated but no updates applied |

```YAML
spec:
  updatePolicy:
    updateMode: "Auto"
```

## Disruption Considerations

* The Updater respects `PodDisruptionBudgets` since evicting a pod would violate the PDB, it waits
* In `Auto` mode, all pods in the target may be restarted over time (Use `Initial` mode for workloads that cannot tolerate restarts)
* StatefulSet pods are handled carefully to avoid breaking quorum

## Commands

```Bash
kubectl get vpa -n NAMESPACE
kubectl describe vpa MY_VPA
```


## Checking Eviction Activity
```Bash
kubectl get events -n NAMESPACE | grep Evicted
kubectl describe vpa MY_VPA | grep -A5 "Update Policy"
```

## Minimum Replicas for Safe Eviction

If a Deployment has only 1 replica and no PDB, the Updater may evict the single pod causing brief downtime. Set `minReplicas: 2` or define a [[PodDisruptionBudgets]] to prevent this.

## Checking VPA Mode

```Bash
kubectl get vpa MY_VPA -o jsonpath='{.spec.updatePolicy.updateMode}'
```

The default mode if not specified is `Auto`.
