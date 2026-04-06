
`CoreDNS` is a DNS server in Kubernetes clusters that handles service discovery and name resolution for pods and services

* Replaced `kube-dns` starting in Kubernetes 1.13 for better flexibility and performance
* Runs as pods in the `kube-system` namespace usually with two replicas across nodes for high availability
* Listens on port 53 (UDP/TCP)
* Resolves cluster-internal names by watching the API server for services and endpoints
* Pods query CoreDNS via the cluster's DNS service IP (Usually the 10th IP in the service CIDR) which is configured automatically in `/etc/resolv.conf`

## CoreDNS Configuration

CoreDNS is managed via a ConfigMap (`coredns`) in the `kube-system` namespace

### Corefile

The `Corefile` is `CoreDNS`'s primary configuration file

* Defines how CoreDNS servers behave (ex: *Port listening*, *Authoritative zones*, *Loaded plugins*, *etc.*)
* Contains one or more server blocks 

#### Server Blocks

A server block is a configuration unit in the Corefile that defines a specific DNS server instance

* Specifies the zone that the server instance handles followed by a port
* Multiple zones can share a block

```Text
example.com {
    errors
    file /db.example.com
}

.:53 {
    kubernetes cluster.local
    forward . 8.8.8.8
}
```

## tmp

* Deployed as two Pods for redundancy as part of a replica set within a deployment in the kube-system namespace
* Requires a configuration file (`Corefile`) that contains a number of plugins configured
* The `Corefile` is usually mapped to the CoreDNS pod as a Config Map object
* A service is created to make CoreDNS accessible by other Pods within the cluster (The service is named as `kube-dns` by default in the kube-system namespace)
* The DNS configurations on Pods are usually done automatically by the kubelet (The IP address of the DNS server in the kubelet's configuration file)