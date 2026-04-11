**Network Namespaces** are a Linux kernel feature that provides isolated network stacks for processes where each namespace has its own interfaces, routing table, iptables rules, and sockets

* Kubernetes uses network namespaces to isolate each Pod's networking from the host and from other pods
* Each Pod gets its own [[Network Namespaces]], giving it a unique IP address and a private network stack
* All containers within a Pod share the same network namespace since they communicate via `localhost`

## How Pods Get Their Network Namespace

1. The [[kubelet]] instructs the [[Container Runtime]] to create a new Pod sandbox (pause container)
2. The sandbox creates a new network namespace for the Pod
3. The CNI plugin is called to configure the namespace: add a `veth` pair, assign the pod IP, set up routes
4. All containers in the Pod are started inside the same namespace as the sandbox

## Pause Container

The `pause` container is a minimal process that holds the network namespace open for the Pod's lifetime

* It does nothing except sleep as its only purpose is to be the namespace anchor
* If the pause container dies, the entire pod restarts (losing the namespace)
* Image: `registry.k8s.io/pause:3.9`

## veth Pair

Each pod network namespace is connected to the host network via a **virtual Ethernet pair (veth)**

```
Pod ns: eth0 ◄────────────────► vethXXXXXX (host ns)
                                      │
                                  cbr0 / bridge
                                      │
                                  Other pods / node routing
```

## Inspecting Network Namespaces

```Bash
# List all network namespaces on a node
ip netns list

# List interfaces in a pod's namespace (using container PID)
PID=$(crictl inspect CONTAINER_ID | jq '.info.pid')
nsenter --target $PID --net ip addr

# See the veth pair on the host
ip link show | grep veth
```

## Network Namespace Isolation

* Pods in different namespaces share the same network namespace model (Kubernetes `Namespace` ≠ Linux network namespace)
* NetworkPolicies operate at the pod selector level, not at the Linux namespace level
* A pod with `hostNetwork: true` runs in the host's network namespace (no isolation)
