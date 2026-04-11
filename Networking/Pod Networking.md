**[[Pod Networking]]** describes how pods on the same or different nodes communicate with each other directly using their assigned IP addresses

* The CNI plugin is responsible for implementing pod-to-pod connectivity according to the Kubernetes [[Network Model]]
* Each pod has a unique IP from the cluster's pod CIDR — no NAT is performed between pods

## Same-Node Pod Communication

Pods on the same node communicate via a virtual bridge (e.g., `cbr0`, `cni0`)

1. Pod A sends a packet to Pod B's IP
2. The packet leaves Pod A's `eth0` (a veth endpoint inside the pod namespace)
3. The other end of the veth pair (`vethXXX`) is connected to the node's bridge
4. The bridge forwards the packet to Pod B's veth pair
5. The packet arrives at Pod B's `eth0`

## Cross-Node Pod Communication

Pods on different nodes need routing at the node level which is implemented via the CNI plugin

### Overlay Networking (VXLAN / Flannel)

1. Pod A sends a packet to Pod B (on Node 2) where the destination is in Node 2's pod CIDR
2. The CNI plugin on Node 1 encapsulates the packet in a VXLAN header (UDP port 8472)
3. The outer packet is sent to Node 2's IP
4. Node 2's CNI decapsulates and delivers to Pod B

### Underlay / BGP Routing (Calico)

1. Each node advertises its pod CIDR via BGP to a route reflector or directly to peers
2. Pod traffic is routed at the IP layer as there's no encapsulation overhead
3. Requires the underlying network to support BGP or host routes

## Pod IP Assignment

Pod IPs are assigned by the CNI plugin's IPAM module when the pod is created

```Bash
# View pod IPs
kubectl get pods -o wide

# Check CNI configuration on a node
cat /etc/cni/net.d/10-bridge.conf
ls /opt/cni/bin/
```

## Pod-to-Pod Connectivity Test

```Bash
# Get target pod IP
POD_IP=$(kubectl get pod TARGET_POD -o jsonpath='{.status.podIP}')

# Test from another pod
kubectl exec SOURCE_POD -- curl http://$POD_IP:PORT
kubectl exec SOURCE_POD -- ping -c3 $POD_IP
```

## Debugging Pod Networking

```Bash
# Check if a pod has received an IP
kubectl get pod POD_NAME -o jsonpath='{.status.podIP}'

# Check CNI binary location
ls /opt/cni/bin/

# Check CNI config
cat /etc/cni/net.d/*.conf
```

If a pod is stuck in `ContainerCreating`, CNI errors appear in `kubectl describe pod` under Events.
