
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

## PVC Binding

Kubernetes binds a PVC to a PV that satisfies all of the following:
* `accessModes` are compatible
* Requested storage size ≤ PV capacity
* `storageClassName` matches (or both are empty)
* `volumeMode` matches

If no PV is available and a [[Storage Classes]] is specified, a new PV is dynamically provisioned.

## PVC in a Pod

```YAML
spec:
  volumes:
    - name: data-volume
      persistentVolumeClaim:
        claimName: my-pvc
  containers:
    - name: app
      volumeMounts:
        - name: data-volume
          mountPath: /data
```

## Commands

```Bash
kubectl get pvc [-n NAMESPACE]
kubectl describe pvc PVC_NAME
kubectl delete pvc PVC_NAME
```

## PVC Expansion

Resize an existing PVC (StorageClass must have `allowVolumeExpansion: true`):

```YAML
spec:
  resources:
    requests:
      storage: 20Gi    # Increase from original size
```

```Bash
kubectl patch pvc my-pvc -p '{"spec":{"resources":{"requests":{"storage":"20Gi"}}}}'
```


## PVC Annotations for Dynamic Provisioning
```YAML
metadata:
  annotations:
    volume.beta.kubernetes.io/storage-provisioner: ebs.csi.aws.com
```
For dynamic PVCs, Kubernetes adds this annotation automatically once a provisioner creates the PV.

## Access Mode Notes

* `ReadWriteOnce` is the most commonly supported mode across cloud block storage (AWS EBS, GCP PD, Azure Disk)
* `ReadWriteMany` requires a shared filesystem: NFS, EFS, Azure Files, or Ceph RBD

## Deleting PVCs

```Bash
kubectl delete pvc PVC_NAME -n NAMESPACE
```

If a PVC is in `Terminating` state, it means a pod is still mounting it. Delete the pod first, then the PVC will be garbage collected.
