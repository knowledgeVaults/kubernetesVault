**[[Architecture/Leader Election]]** is the process by which distributed system components select one active instance to be responsible for performing a specific task, while others remain as passive standbys

* Prevents split-brain scenarios where multiple replicas simultaneously act as the authority
* Enables high availability: if the leader fails, one of the standbys can take over quickly
* Used by Kubernetes control plane components such as [[kube-controller-manager]] and [[kube-scheduler]] when running in multi-master setups

## Kubernetes Leader Election Mechanism

Kubernetes implements leader election using **Lease objects** stored in the [[kube-apiserver]] ([[etcd]])

* Each candidate component tries to atomically acquire or renew a `Lease` resource in the `kube-system` namespace
* The holder of the Lease is the current leader; it must renew the Lease before it expires
* If the leader fails to renew, the Lease expires and another candidate acquires it
* Lease-based election replaced the older annotation-based approach (ConfigMaps/Endpoints) starting in Kubernetes 1.14

## Lease Object

```YAML
apiVersion: coordination.k8s.io/v1
kind: Lease
metadata:
  name: kube-controller-manager
  namespace: kube-system
spec:
  holderIdentity: master-node-1_UUID
  leaseDurationSeconds: 15
  acquireTime: "2024-01-01T00:00:00Z"
  renewTime: "2024-01-01T00:00:10Z"
  leaderTransitions: 3
```

## Key Parameters

| Parameter | Description |
|---|---|
| `leaseDurationSeconds` | How long the Lease is valid without renewal |
| `renewTime` | Timestamp of last successful renewal |
| `holderIdentity` | Unique ID of the current leader instance |
| `leaderTransitions` | Number of times leadership has changed |

## Behavior During Failures

* If the leader crashes, its Lease expires after `leaseDurationSeconds` (default 15s)
* A standby candidate detects the expiry and atomically claims the Lease via the API server
* The new leader begins processing work; clients experience a brief pause during the transition

## Inspecting the Current Leader

```Bash
kubectl get lease kube-controller-manager -n kube-system -o yaml
kubectl get lease kube-scheduler -n kube-system -o yaml
```

The `holderIdentity` field identifies which node currently holds the leader lease.
