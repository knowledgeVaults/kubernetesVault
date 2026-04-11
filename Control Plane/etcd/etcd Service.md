The **[[etcd]] Service** refers to how the etcd cluster is deployed and exposed within a Kubernetes cluster

* In clusters bootstrapped with `[[kubeadm]]`, etcd runs as a **Static Pod** managed directly by the [[kubelet]] on each control plane node
* In some managed Kubernetes offerings (GKE, EKS), etcd is an external managed service not visible to the user
* etcd listens on two ports: `2379` (client communication) and `2380` (peer-to-peer communication between etcd members)

## Static Pod [[Deployments]] (kubeadm)

When etcd is deployed as a Static Pod, its manifest lives at `/etc/kubernetes/manifests/etcd.yaml`

* The kubelet reads this manifest at startup and ensures the etcd pod is always running
* The pod is not managed by the [[kube-apiserver]] — it exists independently on each control plane node
* TLS certificates for etcd are stored in `/etc/kubernetes/pki/etcd/`

## Key etcd Ports

| Port | Purpose |
|---|---|
| `2379` | Client-to-etcd communication (API server connects here) |
| `2380` | etcd peer-to-peer traffic (Raft replication and [[Control Plane/etcd/Leader Election]]) |

## Viewing etcd Service Status

```Bash
# View the static pod manifest
cat /etc/kubernetes/manifests/etcd.yaml

# Check etcd pod status
kubectl get pod -n kube-system -l component=etcd

# Check etcd logs
kubectl logs -n kube-system etcd-NODENAME

# Check etcd member health
etcdctl member list   --endpoints=https://127.0.0.1:2379   --cacert=/etc/kubernetes/pki/etcd/ca.crt   --cert=/etc/kubernetes/pki/etcd/server.crt   --key=/etc/kubernetes/pki/etcd/server.key
```

## Stacked vs External etcd

| Topology | Description |
|---|---|
| **Stacked** | etcd runs on the same control plane nodes as other components (default kubeadm setup) |
| **External** | etcd runs on separate dedicated nodes, isolating failure domains from the API server |

## Service Discovery for etcd in kubeadm

The kube-apiserver locates etcd via the `--etcd-servers` flag in its static pod manifest

```Bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep etcd-servers
# - --etcd-servers=https://127.0.0.1:2379
```

In multi-master setups, all etcd member URLs are listed comma-separated in this flag.
