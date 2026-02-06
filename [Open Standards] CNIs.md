# Kubernetes: Container Network Interfaces

CNIs are plugins that implement networking within your Kubernetes cluster

* Kubernetes relies on CNIs to implement networking capabilities within a cluster since Kubernetes doesn't come with built-in networking
* The supported CNI plugins are stored in the CNI bin directory (`/opt/cni/bin/`) as executables

<br>

# CNI Plugin Configuration File

* Typically configured in the kubelet service on each node in the cluster

Single plugin configuration
```JSON
{
  "cniVersion": "1.1.0",
  "name": "mynet",
  "type": "bridge",
  "bridge": "cni0",
  "isGateway": true,
  "ipMasq": true,
  "ipam": {
    "type": "host-local",
    "subnet": "10.1.0.0/16",
    "routes": [
      { "dst": "0.0.0.0/0" }
    ]
  }
}

```

* `cniVersion` refers to the CNI spec version understood by the plugin and runtime
* `name` refers to the logical name of the CNI network that's used by the runtime
* `type` refers to the type of IP address management used for pods
* `isGateway` is a boolean flag specific to certain plugins that instructs them to configure the network interface as a gateway for the container
* `ipMasq` specifies whether or not the plugin should set up IP masquerading (source NAT) on the host for traffic originating from the container's subnet
* `ipam` refers to how IP addresses and routes are assigned

<br>

Multiple plugin configurations in a chain
```JSON
{
  "cniVersion": "1.1.0",
  "name": "mynet",
  "plugins": [
    {
      "type": "bridge",
      "bridge": "cni0",
      "isGateway": true,
      "ipMasq": true,
      "ipam": {
        "type": "host-local",
        "subnet": "10.1.0.0/16"
      }
    },
    {
      "type": "portmap",
      "capabilities": { "portMappings": true }
    }
  ]
}
```

* `plugins` refer to an array of plugin configs that the runtime will execute in order
