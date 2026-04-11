**Kubernetes Networking** defines how communication works between all entities in a cluster

* Kubernetes enforces a flat [[Network Model]]: every pod must be able to reach every other pod directly by IP without NAT
* This is fundamentally different from traditional VM networking where each host manages its own address space

## The Four Networking Problems Kubernetes Solves

1. **Container-to-container** within the same Pod: via shared [[Network Namespaces]] (loopback `lo`)
2. **Pod-to-Pod** across nodes: implemented by the CNI plugin (overlay or underlay routing)
3. **Pod-to-Service**: implemented by [[kube-proxy]] (iptables/IPVS DNAT rules)
4. **External-to-Service**: implemented by [[NodePort]], [[LoadBalancer]], or [[Ingress]]

## Key Networking Components

| Component | Role |
|---|---|
| **CNI Plugin** | Implements pod CIDR routing between nodes |
| **kube-proxy** | Programs iptables/IPVS rules for Service-to-pod routing |
| **[[CoreDNS]]** | Resolves service and pod names to IPs |
| **[[kube-dns]] Service** | [[ClusterIP]] (`10.96.0.10`) that all pods use as their DNS resolver |
| **Ingress Controller** | Routes external HTTP/S traffic to internal Services |

## Network Model Requirements (CNI Spec)

* Every pod gets a unique IP address cluster-wide
* Pods on the same node and pods on different nodes can communicate using pod IPs — no NAT
* Agents on a node (e.g., [[kubelet]], kube-proxy) can communicate with all pods on that node

## Topics

* [[DNS]]: Service and pod name resolution
* [[Node Networking]]: Per-node network setup and required ports
* [[Pod Networking]]: How pod-to-pod communication works
* [[Service Networking]]: How Service ClusterIPs and NodePorts work
* [[Network Namespaces]]: Linux network namespace isolation for pods
* [[Network Model]]: Detailed CNI requirements
* [[IPAM]]: IP address assignment for pods and services

## Inspecting Cluster Network Configuration

```Bash
# View pod CIDR per node
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name} {.spec.podCIDR}{"
"}{end}'

# View service CIDR (from kube-apiserver flags)
kubectl cluster-info dump | grep service-cluster-ip-range
```
