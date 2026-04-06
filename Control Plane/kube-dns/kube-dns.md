
`kube-dns` is the legacy Kubernetes Service name that exposes the cluster's DNS server for service discovery

* Automatically creates a DNS record for all resources that get created
* Resources within the same namespace can all reach each other using only the hostname
* DNS records aren't enabled for Pods by default but can be enabled

| Hostname                         | Namespace | Type | Root          | IP Address |
| -------------------------------- | --------- | ---- | ------------- | ---------- |
| HOSTNAME                         | NAMESPACE | svc  | cluster.local | IP_ADDRESS |
| IP_ADDRESS (ex: *192-168-10-10*) | NAMESPACE | pod  | cluster.local | IP_ADDRESS |
