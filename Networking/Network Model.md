The **Kubernetes [[Network Model]]** defines the fundamental networking contract that all Kubernetes clusters must satisfy, regardless of the CNI plugin used

* Implemented by the CNI plugin installed on each node
* Provides a flat, routable address space where all pods can communicate without NAT

## Core Requirements

The Kubernetes network model mandates three rules:

1. **Pod-to-Pod without NAT**: any pod can reach any other pod in the cluster using the target pod's IP address, without the source IP being translated
2. **Node-to-Pod without NAT**: agents on a node ([[kubelet]], monitoring tools) can reach any pod on that node using the pod's IP
3. **Pod sees its own IP as others see it**: a pod's IP as seen from inside the pod matches the IP other pods use to reach it (no SNAT)

## Address Spaces

Kubernetes uses three distinct non-overlapping IP address spaces

| Space | Purpose | Typical Range |
|---|---|---|
| Node IPs | Physical host addresses on the node network | 192.168.0.0/16 |
| Pod CIDR | Addresses assigned to pod network interfaces | 10.244.0.0/16 |
| Service CIDR | Virtual IPs for Services (ClusterIPs) | 10.96.0.0/12 |

These ranges must not overlap with each other or with the underlying node network.

## CNI Plugin Responsibility

The CNI plugin (Calico, Cilium, Flannel, etc.) is responsible for implementing the model

* Creates a virtual network interface (e.g., `eth0`) in the pod's [[Network Namespaces]] with its pod IP
* Sets up routes so packets from pod A to pod B's IP are forwarded correctly — even across nodes
* May use overlay (VXLAN, geneve) or underlay (BGP, VPC routing) techniques to achieve cross-node routing

## Host Network Pods

Pods with `spec.hostNetwork: true` bypass the pod network model

* They share the node's network namespace and use the node's IP directly
* Required for system-level pods that need access to host interfaces (e.g., network plugins, monitoring agents)

## Checking That the Model Is Satisfied

```Bash
# Test pod-to-pod connectivity (no NAT)
SRC_POD=pod-a; DST_IP=$(kubectl get pod pod-b -o jsonpath='{.status.podIP}')
kubectl exec $SRC_POD -- ping -c3 $DST_IP
```

A successful ping confirms the CNI plugin correctly implements the flat network model across nodes.
