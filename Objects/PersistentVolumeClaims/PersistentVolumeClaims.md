
* Every PVC is bound to a single PV
* The PVC will remain in a pending state if no PV is available

```YAML
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: PVC_NAME
spec:
  accessModes:
  - ACCESS_MODE
  resources:
    requests:
      storage: STORAGE_SIZE
  storageClass: STORAGE_CLASS_NAME
```

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: POD_NAME
spec:
  containers:
  - [...]
    volumes:
    - name: VOLUME_NAME
      persistentVolumeClain:
        claimName: PVC_NAME
```