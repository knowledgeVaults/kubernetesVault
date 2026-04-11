The **[[Container Storage Interface]] (CSI)** is a standard API that enables storage vendors to develop plugins once and have them work across all container orchestrators that implement the spec

* Decouples storage implementation from the Kubernetes core — new storage backends are added as out-of-tree drivers without core changes
* Replaces the older in-tree volume plugins (e.g., `awsElasticBlockStore`, `gcePersistentDisk`) which are being removed from the core
* A CSI driver consists of a **Controller Plugin** (cluster-level operations) and a **Node Plugin** (per-node operations)

## CSI Driver Components

### Controller Plugin ([[Deployments]] / [[Stateful Sets]])

Handles cluster-level storage operations via cloud/storage APIs

| Operation | RPC | Description |
|---|---|---|
| Create volume | `CreateVolume` | Provision a new storage volume |
| Delete volume | `DeleteVolume` | Deprovision a volume |
| Attach volume | `ControllerPublishVolume` | Attach volume to a node |
| Detach volume | `ControllerUnpublishVolume` | Detach volume from a node |

### Node Plugin ([[Daemon Sets]])

Handles per-node storage operations on each worker node

| Operation | RPC | Description |
|---|---|---|
| Stage volume | `NodeStageVolume` | Mount volume to a staging directory |
| Publish volume | `NodePublishVolume` | Bind-mount from staging to pod volume path |
| Unstage/unpublish | `NodeUnstageVolume` / `NodeUnpublishVolume` | Unmount on pod deletion |

## CSI Driver Registration

CSI node plugins register with the [[kubelet]] via a Unix socket at `/var/lib/kubelet/plugins/`

```Bash
ls /var/lib/kubelet/plugins/   # List registered CSI drivers
kubectl get csidrivers          # List installed CSI drivers cluster-wide
kubectl get csinodes            # List node-level CSI driver topology info
```

## Example: Using a CSI [[Storage Classes]]

```YAML
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
parameters:
  type: gp3
  encrypted: "true"
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```
