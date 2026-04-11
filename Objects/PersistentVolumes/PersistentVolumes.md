
```YAML
apiVersion: v1
kind: PersistentVolume
metadata:
  name: PV_NAME
spec:
  accessModes:
  - ACCESS_MODE
  capacity:
    storage: STORAGE_CAPACITY
  # Option 1: Host
  hostPath:
    path: VOLUME_PATH
  # Option 2: EBS
  awsElasticBlockStore:
    volumeID: VOLUME_ID
    fsType: FS_TYPE
```

| Access Mode     | Description |
| --------------- | ----------- |
| `ReadOnlyMany`  |             |
| `ReadWriteOnce` |             |
| `ReadWriteMany` |             |

| Reclaim Policy         | Description |
| ---------------------- | ----------- |
| `Retain`               |             |
| `Delete`               |             |
| `Recycle` (Deprecated) |             |

## Access Modes

| Mode | Description |
|---|---|
| `ReadWriteOnce` (RWO) | Mounted read-write by a single node |
| `ReadOnlyMany` (ROX) | Mounted read-only by many nodes simultaneously |
| `ReadWriteMany` (RWX) | Mounted read-write by many nodes simultaneously |
| `ReadWriteOncePod` (RWOP) | Mounted read-write by a single pod only (1.22+) |

## Reclaim Policies

| Policy | Behaviour after PVC deletion |
|---|---|
| `Retain` | PV is kept; data preserved; admin must manually reclaim |
| `Delete` | PV and the underlying storage asset are automatically deleted |
| `Recycle` | Deprecated; runs `rm -rf` on the volume before rebinding |

## PV Phases

| Phase | Meaning |
|---|---|
| `Available` | Not yet bound to a PVC |
| `Bound` | Bound to a PVC |
| `Released` | PVC was deleted; PV not yet reclaimed |
| `Failed` | Reclamation failed |

## Commands

```Bash
kubectl get pv
kubectl describe pv PV_NAME
kubectl get pv --sort-by=.spec.capacity.storage
```

## Static vs Dynamic Provisioning

* **Static**: admin pre-creates PVs; PVCs bind to matching available PVs
* **Dynamic**: no pre-created PVs; a [[Storage Classes]] with a provisioner creates the PV automatically when a PVC is submitted

## Checking PV-PVC Binding
```Bash
kubectl get pv
kubectl get pvc -n NAMESPACE
# STATUS: Bound = paired; Available = waiting for claim; Released = PVC deleted
```
