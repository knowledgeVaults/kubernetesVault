
* Every Pod should have an IP address
* Every Pod should be able to communicate with every other Pod in the same node
* Every Pod should be able to communicate with every other Pod on other nodes without NAT

tl;dr networking without using networking solutions

Allowing communication between Pods on the same node
```Bash
# Create veth pair
ip link add ...

# Attach veth pair
ip link set ...

# Assign IP address
ip -n NAMESPACE addr add ...
ip -n NAMESPACE route add ...

# Bring up the interface
ip -n NAMESPACE link set ...

```

Allowing communication between Pods on different nodes
```Bash
# Option 1: Add routes on each node
ip route add ... via ...

# Option 2: Add routes on router and use router as default gateway
```

Create your own CNI
```Bash
# cni-demo-script.sh is our CNI

# Create veth pair

# Attach veth pair

# Retrieve free IP address from file
ip = get_free_ip_from_host_local()
# Or manually modify the ipam section in /etc/cni/net.d/net-script.conf

# Assign IP address

# Bring up interface

# Delete veth pair
```