A **[[Container Runtime]]** is the software component responsible for pulling container images and running containerized processes on a host machine

* Must be installed on **every node** in a Kubernetes cluster on both control plane and worker nodes
* The [[kubelet]] communicates with the container runtime via the **[[Container Runtime Interface]] (CRI)**
* Kubernetes removed the built-in Docker support ([[dockershim]]) in 1.24 therefore all runtimes must be CRI-compliant

## Responsibilities

| Responsibility | Detail |
|---|---|
| Image management | Pull, store, and garbage-collect container images |
| Container lifecycle | Create, start, pause, resume, stop, and delete containers |
| [[Network Namespaces]] | Create and configure the pod network namespace |
| Resource isolation | Apply cgroup limits for CPU, memory, and I/O |
| Execution | Spawn container processes using an OCI-compliant runtime (e.g., runc) |

## CRI-Compliant Runtimes

| Runtime           | Socket                            | Notes                                                    |
| ----------------- | --------------------------------- | -------------------------------------------------------- |
| [[containerd]]    | `/run/containerd/containerd.sock` | Default in most distributions; Docker uses it internally |
| `CRI-O`           | `/var/run/crio/crio.sock`         | Purpose-built for Kubernetes; used in OpenShift          |
| `kata-containers` | Via proxy                         | Hardware-isolated containers using lightweight VMs       |

## Runtime Layers

Modern container runtimes are structured in layers

```
kubelet
  └── CRI (gRPC)
       └── containerd (high-level runtime)
              └── containerd-shim
                     └── runc (OCI runtime)
                            └── Linux cgroups + namespaces
```

## Checking the Runtime on a Node

```Bash
# Via kubectl
kubectl get node NODE_NAME -o jsonpath='{.status.nodeInfo.containerRuntimeVersion}'

# Via crictl
crictl info | grep runtimeType
```

## Image Storage

Container images are stored on disk under the runtime's data directory

* **containerd**: `/var/lib/containerd/`
* **CRI-O**: `/var/lib/containers/storage/`

```Bash
crictl images                   # List cached images on the node
crictl rmi IMAGE                # Remove an image
```
