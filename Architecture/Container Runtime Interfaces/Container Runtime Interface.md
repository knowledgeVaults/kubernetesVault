The **[[Container Runtime Interface]] (CRI)** is a plugin interface that enables the [[kubelet]] to use different container runtimes without needing to recompile

* Defined as a gRPC API that any compliant runtime must implement
* The kubelet communicates with the runtime via a Unix socket using the CRI protocol
* Kubernetes removed the built-in Docker support ([[dockershim]]) in 1.24 and now requires a CRI-compliant runtime

## CRI Architecture

```
kubelet ──gRPC──► CRI socket ──► Runtime (containerd / CRI-O)
                                     │
                                   runc (OCI runtime)
                                     │
                                  container processes
```

## CRI gRPC Services

The CRI defines two gRPC services:

### RuntimeService

Manages pod sandboxes and containers

| Method | Purpose |
|---|---|
| `RunPodSandbox` | Create a pod sandbox ([[Network Namespaces]], pause container) |
| `StopPodSandbox` | Stop all containers in a sandbox |
| `RemovePodSandbox` | Delete a sandbox |
| `CreateContainer` | Create a container within a sandbox |
| `StartContainer` | Start a created container |
| `StopContainer` | Stop a running container |
| `ExecSync` | Run a command in a container synchronously |

### ImageService

Manages container images

| Method | Purpose |
|---|---|
| `ListImages` | List available images |
| `PullImage` | Download an image |
| `RemoveImage` | Delete a cached image |

## Configuring the CRI Socket

The kubelet's `--container-runtime-endpoint` flag points to the runtime socket

```Bash
# containerd
--container-runtime-endpoint=unix:///run/containerd/containerd.sock

# CRI-O
--container-runtime-endpoint=unix:///var/run/crio/crio.sock
```

## Supported Runtimes

| Runtime | Socket | Notes |
|---|---|---|
| [[containerd]] | `/run/containerd/containerd.sock` | Default in most distributions |
| CRI-O | `/var/run/crio/crio.sock` | Purpose-built for Kubernetes; used in OpenShift |

## Switching Runtimes

To change from one CRI runtime to another on an existing node:

1. Drain the node: `kubectl drain NODE_NAME --ignore-daemonsets`
2. Stop the old runtime and install the new one
3. Update `/var/lib/kubelet/config.yaml` with the new socket path
4. Restart kubelet: `systemctl restart kubelet`
5. Uncordon: `kubectl uncordon NODE_NAME`
