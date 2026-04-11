**[[Node Networking]]** describes the network configuration required on each Kubernetes node for the cluster to function correctly

* Each node must have at least one network interface, a unique IP, a unique hostname, and a unique MAC address
* Several ports must be open between nodes for control plane communication, [[kubelet]] API access, and [[Pod Networking]]

## Required Open Ports

### Control Plane Nodes

| Port | Protocol | Component | Purpose |
|---|---|---|---|
| `6443` | TCP | [[kube-apiserver]] | API server HTTPS |
| `2379` | TCP | [[etcd]] | Client communication |
| `2380` | TCP | etcd | Peer-to-peer [[Architecture/Leader Election]] |
| `10250` | TCP | kubelet | kubelet API (health, metrics, exec) |
| `10257` | TCP | [[kube-controller-manager]] | Health and metrics endpoint |
| `10259` | TCP | [[kube-scheduler]] | Health and metrics endpoint |

### Worker Nodes

| Port | Protocol | Component | Purpose |
|---|---|---|---|
| `10250` | TCP | kubelet | kubelet API |
| `30000–32767` | TCP/UDP | [[kube-proxy]] | [[NodePort]] service range |

## Interface Setup

```Bash
ip link                         # List all network interfaces
ip addr                         # Show IP addresses
ip addr add IP/MASK dev IFACE   # Assign an IP to an interface
ip route                        # Show routing table
ip route add CIDR via GATEWAY   # Add a static route
```

## Verifying Node Connectivity

```Bash
# Check if required ports are open
netstat -pnlt | grep -E '6443|2379|10250'

# Check ARP table (for node-to-node resolution)
arp

# Check routes between nodes
ip route show
```

## MAC and IP Uniqueness

Kubernetes requires each node to have a unique MAC address and hostname

* Duplicate MACs or hostnames cause issues with node registration and networking
* Common problem in VM cloning scenarios (Always regenerate MACs and reset hostnames on cloned VMs)

## Virtual Interfaces Created by CNI

When a pod is started, the CNI plugin creates a veth pair visible on the host

```Bash
ip link show | grep veth    # List all pod veth endpoints on the host
bridge link                 # Show bridge membership
```

Each `vethXXXXXX` connects a pod's `eth0` to the node bridge (`cbr0` or `cni0`).
