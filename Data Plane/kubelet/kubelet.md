The [[kubelet]] is the primary node agent on every Kubernetes node that bridges control plane decisions with actual container execution

* Self-registers the node with the [[kube-apiserver]] on startup, reporting capacity and status
* Watches for PodSpecs assigned to its node and drives their lifecycle using the [[Container Runtime]] (CRI)
* Reports pod and node health back to the control plane via heartbeats and status updates
* Enforces resource limits and triggers pod eviction when a node faces memory or disk pressure

## Core Responsibilities

| Task | Detail |
|---|---|
| **Pod lifecycle** | Start, stop, restart containers via CRI; enforce restart policies |
| **Health monitoring** | Runs liveness, readiness, and startup probes; restarts failing containers |
| **Resource enforcement** | Applies CPU/memory limits via cgroups; evicts pods under node pressure |
| **Volume management** | Mounts PVCs via CSI drivers; mounts ConfigMaps, Secrets, projected volumes |
| **Status reporting** | Updates `.status` on Pod and Node objects; sends Lease heartbeats |
| **Static Pods** | Manages pods defined as local YAML files without API server involvement |

## Key Flags

| Flag | Description |
|---|---|
| `--config` | Path to KubeletConfiguration file |
| `--container-runtime-endpoint` | CRI socket (e.g., `unix:///run/[[containerd]]/containerd.sock`) |
| `--pod-manifest-path` | Directory for Static Pod manifests |
| `--node-labels` | Labels to add to the Node object on registration |
| `--max-pods` | Maximum number of pods per node (default 110) |

## Kubelet Configuration File

```YAML
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
staticPodPath: /etc/kubernetes/manifests
clusterDNS:
  - 10.96.0.10
clusterDomain: cluster.local
evictionHard:
  memory.available: "100Mi"
  nodefs.available: "10%"
```

## Health and Logs

```Bash
systemctl status kubelet
journalctl -u kubelet -f
curl -sk https://NODE_IP:10250/healthz
```

## Node Conditions

The kubelet reports node conditions to the API server

| Condition | Meaning |
|---|---|
| `Ready` | Node is healthy and can accept pods |
| `MemoryPressure` | Node is running low on memory |
| `DiskPressure` | Node is running low on disk |
| `PIDPressure` | Node has too many running processes |
| `NetworkUnavailable` | [[Node Networking]] is not correctly configured |
