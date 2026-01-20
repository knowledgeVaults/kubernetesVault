# Kubernetes: CoreDNS

CoreDNS is a DNS server deployed as a Deployment  in the `kube-system` namespace and exposed via the `kube-dns` service

* Automatically creates DNS records for pods within the cluster by watching the Kubernetes API for services and endpoints
* Pods will automatically get `/etc/resolv.conf` auto-configured by the kubelet to point to the `kube-dns` ClusterIP as nameserver
* Handles internal resolutions and forwarding external queries upstream
