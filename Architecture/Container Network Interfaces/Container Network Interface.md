The **[[Container Network Interface]] (CNI)** is a specification and set of libraries that define how networking plugins configure network interfaces for container runtimes

* CNI plugins are called by the [[Container Runtime]] (via [[kubelet]]) to set up and tear down [[Pod Networking]]
* A CNI plugin is responsible for: creating the pod [[Network Namespaces]], assigning an IP, setting up routing, and cleaning up on pod deletion
* Kubernetes does not ship with a default CNI — one must be installed before pods can communicate

## How CNI Works in Kubernetes

1. kubelet creates a pod sandbox (pause container with a new network namespace)
2. kubelet calls the CNI plugin via the CNI spec binary interface
3. The CNI plugin receives the network namespace path, container ID, and config from `/etc/cni/net.d/`
4. The plugin creates a veth pair, assigns an IP from the node's pod CIDR, sets up routes, and returns the result

## CNI Configuration

```Bash
# View installed CNI plugins
ls /opt/cni/bin/

# View active CNI configuration
ls /etc/cni/net.d/
cat /etc/cni/net.d/10-bridge.conf
```

## CNI Plugin Types

| Type | Example Plugins | Description |
|---|---|---|
| Overlay | Flannel (VXLAN), Weave | Encapsulates pod traffic in UDP/VXLAN frames |
| Underlay / BGP | Calico, Cilium | Routes pod traffic at the IP layer via BGP or host routes |
| eBPF | Cilium | Kernel-level packet processing; no iptables |

## Built-in CNI Plugins

The CNI spec ships with basic plugins in `/opt/cni/bin/`

* `bridge`: creates a Linux bridge and connects containers to it
* `host-local`: IPAM plugin that allocates IPs from a local range
* `loopback`: creates the loopback (`lo`) interface in the container namespace
* `vlan`, `ipvlan`, `macvlan`: advanced L2/L3 plugins

## Diagnosing CNI Issues

```Bash
# If pod is stuck ContainerCreating:
kubectl describe pod POD_NAME | grep -A5 Events

# Check CNI logs
journalctl -u kubelet | grep -i cni
ls /var/log/containers/
```

## CNI Spec Requirements

* Runtime must create the network namespace before calling the plugin
* Plugin must support `ADD`, `DEL`, and `CHECK` commands
* Plugin must manage IP address assignment (`IPAM`)
* Plugin must return results in the CNI result format (JSON)
