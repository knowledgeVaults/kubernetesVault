**Kubernetes Plugins** are executable programs or webhook-based components that extend specific subsystems of Kubernetes via defined interfaces

* Plugins are the concrete implementations of Kubernetes extension points
* Each subsystem (scheduler, CNI, CSI, CRI) defines a plugin interface that implementations must satisfy

## [[kubectl]] Plugins

`kubectl` supports a plugin system allowing custom subcommands

* Any executable named `kubectl-COMMAND` on the `$PATH` becomes available as `kubectl COMMAND`
* Plugins are distributed via `krew`, the kubectl plugin manager
* Popular plugins: `kubectl-neat` (clean output), `kubectl-tree` (owner reference tree), `kubectl-ctx`/`kubectl-ns` (context/namespace switching)

```Bash
# Install krew
kubectl krew install tree
kubectl tree deployment my-deployment

# List installed plugins
kubectl plugin list
```

## Scheduler Plugins

The [[kube-scheduler]] exposes plugin hooks for each scheduling phase

| Phase | Plugin Examples |
|---|---|
| `PreFilter` | `NodeResourcesFit`, `PodTopologySpread` |
| `Filter` | `NodeAffinity`, `TaintToleration`, `VolumeZone` |
| `Score` | `NodeResourcesBalancedAllocation`, `ImageLocality` |
| `Bind` | `DefaultBinder` |

Custom scheduler plugins are compiled into a custom scheduler binary or registered via `--config` scheduler profiles.

## CNI Plugins

CNI plugins are executed by the [[kubelet]] via the CNI spec binary interface

* Located in `/opt/cni/bin/`
* Called with JSON config from `/etc/cni/net.d/` to set up [[Pod Networking]] on each node

## CSI Plugins (Drivers)

CSI drivers run as pods (typically DaemonSets + Deployments) and register with the kubelet via a Unix socket

* **Node plugin** ([[Daemon Sets]]): handles `NodeStageVolume`, `NodePublishVolume` on each node
* **Controller plugin** ([[Deployments]]): handles `CreateVolume`, `AttachVolume`, `DeleteVolume` via the cloud API

## CRI Plugins

The kubelet communicates with the [[Container Runtime]] via a CRI plugin socket

```Bash
# containerd socket
--container-runtime-endpoint=unix:///run/containerd/containerd.sock
# CRI-O socket
--container-runtime-endpoint=unix:///var/run/crio/crio.sock
```
