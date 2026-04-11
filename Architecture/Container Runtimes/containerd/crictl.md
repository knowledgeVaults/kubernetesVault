[[crictl]] is a CLI tool for interacting directly with CRI-compliant container runtimes for debugging and inspection

* Developed by the Kubernetes SIG-node team and distributed as part of `cri-tools`
* Works with any CRI runtime ([[containerd]], CRI-O) via the gRPC CRI API
* Intended for **debugging and troubleshooting** node-level issues, not for production container management
* Does not manage images or containers the same way Docker or [[nerdctl]] do; it speaks CRI directly

## Configuration

`crictl` needs to know the runtime endpoint to communicate with

```Bash
# Configure permanently
crictl config --set runtime-endpoint=unix:///run/containerd/containerd.sock

# Or via config file /etc/crictl.yaml
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///run/containerd/containerd.sock
timeout: 10
```

## Common Commands

### Containers

```Bash
crictl ps                          # List running containers
crictl ps -a                       # List all containers (including stopped)
crictl inspect CONTAINER_ID        # Inspect container details
crictl logs CONTAINER_ID           # View container logs
crictl exec -it CONTAINER_ID sh    # Execute command in container
crictl rm CONTAINER_ID             # Remove container
crictl stats                       # Container resource usage
```

### Pods

```Bash
crictl pods                        # List pod sandboxes
crictl inspectp POD_ID             # Inspect pod sandbox
crictl stopp POD_ID                # Stop pod sandbox
crictl rmp POD_ID                  # Remove pod sandbox
```

### Images

```Bash
crictl images                      # List images
crictl pull IMAGE                  # Pull an image
crictl rmi IMAGE                   # Remove an image
crictl imagefsinfo                 # Image filesystem info
```

## crictl vs [[kubectl]]

* `crictl` operates at the CRI/runtime level — it sees containers regardless of Kubernetes state
* `kubectl` operates at the Kubernetes API level — it manages objects in [[etcd]]
* Use `crictl` when the [[kubelet]] or [[kube-apiserver]] is not functioning and you need direct node-level access
