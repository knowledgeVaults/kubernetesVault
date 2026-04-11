
A **[[PodDisruptionBudgets]] (PDB)** limits the number of pods that can be simultaneously unavailable during voluntary disruptions such as node drains, cluster upgrades, or rolling deployments

* Protects high-availability workloads from being taken down all at once during maintenance
* Only applies to **voluntary disruptions** (drains, evictions) and not involuntary ones (node crashes)
* The `kubectl drain` command respects PDBs as it waits if evicting a pod would violate the budget

## PDB Manifest

```YAML
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
  namespace: production
spec:
  minAvailable: 2        # At least 2 pods must be available at all times
  selector:
    matchLabels:
      app: my-app
```

## minAvailable vs maxUnavailable

| Field | Meaning |
|---|---|
| `minAvailable: N` | At least N pods must remain available; disruptions are blocked if this would be violated |
| `minAvailable: "50%"` | At least 50% of pods must remain available |
| `maxUnavailable: N` | At most N pods can be unavailable at once |
| `maxUnavailable: "25%"` | At most 25% of pods can be unavailable |

Use either `minAvailable` or `maxUnavailable`, not both.

## Example: Protecting a [[Stateful Sets]]

```YAML
spec:
  maxUnavailable: 1    # Only one replica can be down at a time
  selector:
    matchLabels:
      app: my-database
```

## Effect on Cluster Operations

```Bash
# kubectl drain will block if PDB would be violated
kubectl drain worker-1 --ignore-daemonsets --delete-emptydir-data
# Error: Cannot evict pod "my-app-abc" as it would violate PDB "my-app-pdb"
```

## Commands

```Bash
kubectl get pdb [-n NAMESPACE]
kubectl describe pdb my-app-pdb -n production
```

## PDB and the Cluster Autoscaler

The Cluster Autoscaler respects PDBs during scale-in. A node won't be drained if doing so would violate any PDB. Use `cluster-autoscaler.kubernetes.io/safe-to-evict: "false"` on pods that should never be evicted automatically.

## Checking PDB Status

```Bash
kubectl get pdb my-app-pdb -n production
# ALLOWED DISRUPTIONS shows how many pods can currently be evicted
```

If `ALLOWED DISRUPTIONS` is `0`, no more pods can be evicted until one restarts and becomes Ready again.
