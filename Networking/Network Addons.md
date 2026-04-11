**Network Addons** are components installed on top of the core Kubernetes distribution to provide or extend cluster networking capabilities

* Kubernetes does not ship with a default CNI plugin (At minimum a CNI addon must be installed before pods can communicate)
* Addons are typically deployed as DaemonSets (per-node agents) plus optional Deployments (controllers)
* Choosing the right addon depends on performance requirements, [[Network Policies]] needs, and cloud environment

## CNI Addons

CNI addons implement [[Pod Networking]] and optional Layer 3/4 policy enforcement

| Addon | Networking Model | NetworkPolicy | Notable Feature |
|---|---|---|---|
| **Calico** | BGP or VXLAN overlay | Full L3/L4 | Scalable; widely used in enterprise |
| **Cilium** | eBPF (no iptables) | L3/L4/L7 | Hubble observability; service mesh capabilities |
| **Flannel** | VXLAN overlay | None (needs separate policy engine) | Simple; minimal overhead |
| **Weave Net** | Mesh (VXLAN/fast datapath) | Full L3/L4 | Built-in encryption |
| **Canal** | Flannel networking + Calico policy | Full L3/L4 | Combo addon |

## Installing a CNI Addon

CNI addons are typically installed via `kubectl apply` on a manifest or via a [[Helm]] chart

```Bash
# Calico
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

# Cilium via Helm
helm repo add cilium https://helm.cilium.io/
helm install cilium cilium/cilium --namespace kube-system

# Flannel
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

## DNS Addon ([[CoreDNS]])

CoreDNS is the mandatory DNS addon providing service discovery within the cluster

* Deployed automatically by [[kubeadm]] as a [[Deployments]] in `kube-system`
* All pods have `/etc/resolv.conf` pointing to the CoreDNS [[ClusterIP]]
* Configurable via the `coredns` [[Config Maps]] in `kube-system`

## Metrics Addon ([[Metrics Server]])

Metrics Server is an optional addon enabling `kubectl top` and HPA/VPA autoscaling based on resource usage

```Bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
