
* Each node must have at least one interface connected to a network, an IP address, a unique hostname, and a unique MAC address
* Requires ports 2379, 2380, 6443, 10250, 10257, and 10259 to be open

```Bash
#
ip link

#
ip addr add IP_ADDRESS/SUBNET_MASK dev INTERFACE

#
ip route add IP_ADDRESS/SUBNET_MASK via GATEWAY_IP

#
netstat -pint

# 
ip addr

# 
ip route

# 
arp

#
route
```