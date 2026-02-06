# Kubernetes: kube-dns

Kube-dns is the Kubernetes cluster service that provides DNS-based service discovery by automatically creating DNS records for Services and Pods

* Every new pod gets `/etc/resolv.conf` auto-configured to point to the kube-dns ClusterIP as nameserver
* The default root domain for a Kubernetes cluster is `cluster.local`