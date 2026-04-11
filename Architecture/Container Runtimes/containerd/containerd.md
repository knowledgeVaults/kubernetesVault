[[containerd]] is an industry-standard, CRI-compliant [[Container Runtime]] that manages the complete container lifecycle on a host

* Originally extracted from Docker and donated to the CNCF in 2017
* The default container runtime for Kubernetes clusters since [[dockershim]] was removed in 1.24
* Implements the **[[Container Runtime Interface]] (CRI)** so [[kubelet]] can communicate with it directly
* Delegates the actual container process creation to `runc` via the **OCI runtime spec**

## containerd Architecture

containerd is structured as a daemon with a modular plugin architecture

* Exposes a gRPC API consumed by the kubelet over a Unix socket (`/run/containerd/containerd.sock`)
* **Snapshotter**: manages image layers and filesystem mounts (overlayfs by default)
* **Content Store**: stores image blobs (layers, manifests, configs) in `/var/lib/containerd`
* **Task Service**: handles container lifecycle (create, start, pause, stop, delete)
* Delegates process creation to `runc` (or any OCI runtime) via the **shim** model

## containerd Shim

Each container runs with a lightweight shim process (`containerd-shim-runc-v2`) between containerd and the container

* The shim keeps the container running even if the containerd daemon restarts
* Handles stdio streams and exit status reporting back to containerd
* Enables zero-downtime upgrades of containerd without killing running containers

## Configuration

containerd is configured via `/etc/containerd/config.toml`

```toml
[plugins."io.containerd.grpc.v1.cri"]
  [plugins."io.containerd.grpc.v1.cri".containerd]
    snapshotter = "overlayfs"
    [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
      runtime_type = "io.containerd.runc.v2"
```

## CLI Tools

| Tool | Purpose |
|---|---|
| `ctr` | Low-level containerd CLI (not CRI-aware) |
| `[[nerdctl]]` | Docker-compatible CLI for containerd |
| `[[crictl]]` | CRI-focused debugging tool (not containerd-specific) |

## Runtime Classes

containerd supports multiple OCI runtimes via `RuntimeClass` objects

* `runc`: standard Linux containers using cgroups and namespaces
* `kata-containers`: hardware-virtualized containers for stronger isolation
* `gVisor` (`runsc`): user-space kernel sandbox for reduced kernel attack surface
