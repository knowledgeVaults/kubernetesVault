[[nerdctl]] is a Docker-compatible CLI for [[containerd]] that provides a familiar `docker`-like interface while working natively with containerd`

* Drop-in replacement for the `docker` CLI (Note: most Docker commands work with `nerdctl` without modification)
* Supports the full Docker workflow: `build`, `run`, `push`, `pull`, `compose`
* Communicates with containerd directly (not via Docker daemon) since no Docker dependency required

## Installation

```Bash
# Download nerdctl (full bundle includes CNI plugins and rootless support)
wget https://github.com/containerd/nerdctl/releases/latest/download/nerdctl-full-VERSION-linux-amd64.tar.gz
tar -xzf nerdctl-full-*.tar.gz -C /usr/local
```

## Basic Commands (Docker-compatible)

```Bash
nerdctl run -d --name nginx nginx:latest
nerdctl ps
nerdctl stop nginx
nerdctl rm nginx
nerdctl images
nerdctl pull alpine:latest
nerdctl rmi alpine:latest
nerdctl logs CONTAINER_ID
nerdctl exec -it CONTAINER_ID sh
```

## Building Images

```Bash
nerdctl build -t my-app:v1 .
nerdctl push registry.example.com/my-app:v1
```

## nerdctl Compose

```Bash
nerdctl compose up -d
nerdctl compose down
nerdctl compose ps
```

## Namespaces

Like `ctr`, nerdctl supports containerd namespaces

```Bash
nerdctl --namespace k8s.io ps    # List Kubernetes containers
nerdctl -n k8s.io images         # List Kubernetes images
```

## Rootless Mode

nerdctl supports running containers without root, using rootless containerd

```Bash
containerd-rootless-setuptool.sh install
nerdctl run --rm alpine echo "running rootless"
```

## nerdctl vs docker vs [[crictl]]

| Feature | docker | nerdctl | crictl |
|---|---|---|---|
| Docker-compatible | Yes | Yes | No |
| Compose support | Yes | Yes | No |
| CRI-aware | No | No | Yes |
| Kubernetes namespaces | No | Optional | Yes |

## Inspecting Kubernetes Containers

```Bash
# List pods running in the cluster (reads from k8s.io namespace)
nerdctl -n k8s.io ps --format "{{.Names}}	{{.Status}}"

# View container logs
nerdctl -n k8s.io logs CONTAINER_ID
```

This is useful when `[[kubectl]]` is unavailable but containerd is accessible on the node.
