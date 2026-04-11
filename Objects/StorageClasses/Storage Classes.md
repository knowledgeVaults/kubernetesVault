
* Enables dynamic provisioning of volumes
* Allows you to create different classes of service

## GCP

```YAML
apiVersion: storage.k8s.io/va
kind: StorageClass
metadata:
  name: STORAGE_CLASS_NAME
provisioner: PROVISIONER 
parameters:
  type: DISK_TYPE
  replication-type: REPLICATION_TYPE
```

## [[Storage Classes]] Parameters

Parameters are provisioner-specific and passed directly to the storage backend

```YAML
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: ebs.csi.aws.com
parameters:
  type: gp3
  iops: "3000"
  throughput: "125"
  encrypted: "true"
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
```

## volumeBindingMode

| Mode | Behaviour |
|---|---|
| `Immediate` | PV is provisioned immediately when PVC is created |
| `WaitForFirstConsumer` | PV is provisioned only when a Pod using the PVC is scheduled (recommended; respects topology) |

## Default StorageClass

One StorageClass can be marked as default (PVCs without a `storageClassName` use it automatically):

```YAML
metadata:
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
```

```Bash
kubectl get storageclass
kubectl get sc    # Short alias
```

## Common Provisioners

| Provisioner | Cloud/Storage |
|---|---|
| `ebs.csi.aws.com` | AWS EBS |
| `pd.csi.storage.gke.io` | GCP Persistent Disk |
| `disk.csi.azure.com` | Azure Managed Disk |
| `nfs.csi.k8s.io` | NFS |
| `rook-ceph.rbd.csi.ceph.com` | Ceph RBD (Rook) |


## Listing StorageClasses

```Bash
kubectl get storageclass
kubectl describe storageclass fast-ssd
kubectl get sc -o wide
```

Default StorageClass is shown with `(default)` in the NAME column.

## WaitForFirstConsumer

Using `volumeBindingMode: WaitForFirstConsumer` ensures the PV is created in the same availability zone as the pod, preventing cross-zone volume attachment errors that cause pods to remain in `Pending` state indefinitely.

## Dynamic vs Static Provisioning Summary

With `volumeBindingMode: WaitForFirstConsumer`, the provisioner waits until a pod requesting the PVC is scheduled. This prevents orphaned PVs from being created in the wrong availability zone before the workload is placed.
