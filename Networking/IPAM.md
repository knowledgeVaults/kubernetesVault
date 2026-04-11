**IPAM (IP Address Management)** in Kubernetes refers to the process of assigning, tracking, and reclaiming IP addresses for pods and services within the cluster

* The **CNI plugin** is responsible for pod IPAM as it assigns an IP from the node's allocated pod CIDR when a pod starts, and releases it when the pod stops
* The **[[kube-apiserver]] / [[kube-controller-manager]]** manages node CIDR allocation (assigning each node a subnet of the broader pod CIDR)
* The **kube-apiserver** assigns Service ClusterIPs from the Service CIDR (`--service-cluster-ip-range`)

## CIDR Allocation Hierarchy

```
Cluster Pod CIDR: 10.244.0.0/16
  ├── Node 1: 10.244.0.0/24  (256 pod IPs)
  ├── Node 2: 10.244.1.0/24
  └── Node 3: 10.244.2.0/24

Service CIDR: 10.96.0.0/12
  └── ClusterIPs assigned from this range per Service object
```

## CNI IPAM Plugins

CNI plugins use IPAM sub-plugins to manage IP assignments

| IPAM Plugin | Behaviour |
|---|---|
| `host-local` | Allocates IPs from a static range stored on disk per node; simple and widely used |
| `dhcp` | Obtains IPs from an external DHCP server |
| `calico-ipam` | Calico's own IPAM with block-based allocation and IP pools |
| `cilium` | eBPF-based IPAM integrated with Cilium's routing model |

## Checking Node CIDR Allocation

```Bash
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{" "}{.spec.podCIDR}{"
"}{end}'
```

## Service IP Management

Service ClusterIPs are allocated by the kube-apiserver from the `--service-cluster-ip-range`

```Bash
kubectl get svc -A -o custom-columns=NAME:.metadata.name,CLUSTERIP:.spec.clusterIP
```

## IP Exhaustion

If a node's pod CIDR is exhausted, new pods cannot be scheduled on that node

* Typical `/24` gives 254 usable IPs — sufficient for most nodes (default max-pods is 110)
* Larger clusters should use `/16` or wider pod CIDRs to avoid exhaustion at scale
* CNI plugins like Calico allow flexible IP pool sizes and can reassign blocks dynamically

## Troubleshooting IP Exhaustion

```Bash
# Check how many IPs are in use on a node
kubectl get pods --all-namespaces --field-selector spec.nodeName=NODE_NAME | wc -l

# Check the node's pod CIDR
kubectl get node NODE_NAME -o jsonpath='{.spec.podCIDR}'
```

If a node's `/24` is full, new pods will fail to start with a CNI IPAM error.
