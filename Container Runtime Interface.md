
* A standard that defines how an orchestration solution would communicate with container runtimes 

tl;dr
```
Container runtime must create network namespace
Identify the network the container must attach to
Container Runtime to invoke Network Pluging (bridge) when container is added
Container Runtime to invoke Network Plugin (bridge) when container is deleted
JSON format of the Network Configuration
Must support command line arguments (ADD, DEL, CHECK)
Must support parameters container id, network ns, etc.
Must manage IP Address assignment to Pods
Must return results in a specific format
```

CNI comes with a set of plugins already:
* BRIDGE
* VLAN
* IPVLAN
* MACVLAN
* WINDOWS
* host-local
* DHCP

