
An `Ingress` is an object that exposes HTTP and HTTPS routes from outside the cluster to Services within the cluster

* Acts similarly as a L7 load balancer

Limitations
* Can pose a challenge in multi-tenant environments (Lacks support for multi-tenancy)
* Only supports HTTP-based rules (*Host matching*, *Path matching*, *etc.*) and no support for additional features (*TCP/UDP routing*, *Traffic splitting/weighting*, *Header manipulation*, *etc.*)