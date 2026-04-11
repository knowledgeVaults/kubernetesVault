ctr is the low-level CLI client bundled with [[containerd]] that directly interacts with the containerd daemon via its gRPC API

* Intended for **debugging and testing** containerd directly (Not for everyday cluster management)
* Does **not** implement the CRI spec but rather it talks to containerd's native API, bypassing CRI
* Use [[crictl]] for CRI-aware debugging or [[nerdctl]] for a Docker-compatible experience

## Key Differences

| Tool | CRI-aware | Docker-compatible | Recommended for |
|---|---|---|---|
| `ctr` | No | No | Low-level containerd debugging |
| `crictl` | Yes | No | CRI/runtime debugging |
| `nerdctl` | No | Yes | Developer container management |

## Namespace Concept in ctr

containerd uses **namespaces** to isolate resources; Kubernetes uses the `k8s.io` namespace

```Bash
# List all containerd namespaces
ctr namespaces list

# All Kubernetes containers are in the k8s.io namespace
ctr -n k8s.io containers list
```

## Common Commands

```Bash
# List images
ctr -n k8s.io images list

# Pull an image
ctr -n k8s.io images pull docker.io/library/nginx:latest

# List containers
ctr -n k8s.io containers list

# List running tasks (actual container processes)
ctr -n k8s.io tasks list

# Delete a container
ctr -n k8s.io containers delete CONTAINER_ID

# Run a container (low-level)
ctr -n k8s.io run docker.io/library/nginx:latest my-nginx
```

## Snapshots

```Bash
# List snapshots (image layers)
ctr -n k8s.io snapshots list
```

## When to Use ctr

* Verifying that containerd is receiving images correctly at the daemon level
* Debugging image pull failures that are invisible at the CRI/[[kubelet]] level
* Testing containerd plugin or configuration changes before applying to the cluster

## Note on Production Use

`ctr` is **not recommended** for managing containers in production Kubernetes clusters. Use `[[kubectl]]` for Kubernetes-managed workloads, `crictl` for CRI-level debugging, or `nerdctl` for direct container management outside Kubernetes.

## Checking containerd daemon status

```Bash
systemctl status containerd
journalctl -u containerd -f
```

The containerd service must be running before kubelet can start managing pods on the node.
