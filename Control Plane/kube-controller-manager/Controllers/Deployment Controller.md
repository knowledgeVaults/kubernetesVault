The **[[Deployments]] Controller** is responsible for reconciling the desired state of `Deployment` objects with the actual cluster state

* Watches the [[kube-apiserver]] for `Deployment` create/update/delete events via the [[List+Watch]] mechanism
* Manages `[[ReplicaSets]]` objects as intermediaries as it does not directly manage Pods
* Implements rolling update and rollback strategies by controlling which ReplicaSets are active

## Reconciliation Loop

The Deployment Controller's core loop continuously compares desired vs actual state

1. User applies or updates a `Deployment` object
2. The controller notices the change via a watch event
3. It creates a new `ReplicaSet` matching the updated Pod template (new hash label)
4. It gradually scales up the new ReplicaSet and scales down the old one according to the rollout strategy
5. Once rollout is complete, the old ReplicaSet is retained (scaled to 0) for rollback purposes

## ReplicaSet Ownership

The Deployment Controller owns ReplicaSets via the `ownerReferences` field

```YAML
ownerReferences:
  - apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
    uid: DEPLOYMENT_UID
    controller: true
    blockOwnerDeletion: true
```

* Orphaned ReplicaSets (no matching Deployment) are not managed by this controller
* The `revisionHistoryLimit` field on the Deployment controls how many old ReplicaSets are retained

## Rolling Update Strategy

```YAML
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1
    maxSurge: 1
```

* `maxUnavailable`: maximum number of pods that can be unavailable during the rollout
* `maxSurge`: maximum number of extra pods that can be created above the desired count
* The controller ensures these constraints are respected at each step of the rollout

## Key Conditions

The Deployment Controller populates `.status.conditions` on the Deployment object

| Condition | Meaning |
|---|---|
| `Available` | At least `minReadySeconds` pods are ready |
| `Progressing` | A rollout is in progress or recently completed |
| `ReplicaFailure` | A ReplicaSet failed to create pods |
