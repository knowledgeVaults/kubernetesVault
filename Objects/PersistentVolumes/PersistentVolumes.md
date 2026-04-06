
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
