
* The underlying host has visibility on all of the processes running on it and inside containers 

Create a new network namespace
```Bash
ip netns add NAMESPACE
```

List all network namespces
```Bash
ip netns
```

List all interfaces within a namespace
```Bash
#
ip netns exec NAMESPACE ip link

#
ip -n NAMESPACE link
```

tmp
```Bash
ip link add INTERFACE_NAME type INTERFACE_TYPE peer name PEER_INTERFACE_NAME
```

Attach an interface to a namespace
```Bash
ip link set INTERFACE_NAME netns NAMESPACE
```

Attach an IP address to a namespace
```Bash
ip -n NAMESPACE addr add IP_ADDRESS/SUBNET_MASK dev INTERFACE_NAME
```

Activate an interface
```Bash
ip -n NAMESPACE link set INTERFACE_NAME up
```

View ARP entries in a namespace
```Bash
ip netns exec NAMESPACE arp
```

Create an interface to be used as a virtual switch  
```Bash
# Create
ip link add INTERFACE_NAME type bridge

# Activate 
ip link set dev INTERFACE_NAME up

# Assign network address to virtual switch
ip addr add NETWORK_ADDRESS/SUBNET_RANGE dev VIRTUAL_SWITCH_INTERFACE
```

Link namespace bridge to virtual switch
```Bash
# Link namespace with bridge interface
ip link add INTERFACE_NAME type INTERFACE_TYPE peer name PEER_INTERFACE_NAME

# Assign IP address to namespace's bridge interface
ip -n NAMESPACE addr add IP_ADDRESS dev INTERFACE_NAME
```

Add NAT
```Bash
iptables -t nat -A POSTROUTING -s NETWORK_IP/SUBNET_MASK -j MASQUERADE
```

Port forwarding
```Bash
iptables -t nat -A PREROUTING --dport PORT_NUMBER --to-destination IP_ADDRESS:PORT_NUMBER -j DNAT
```

tl;dr
```
1. Create network namespace
2. Create bridge network/interface
3. Create veth pairs (pipe, virtual cable)
4. Attach veth to namespace
5. Attach other veth to bridge
6. Assign IP addresses
7. Bring the interfaces up
8. Enable NAT-IP masquerade
```