# Kubernetes: Pod Records

Pod records refer to the DNS records that are created for Pods inside the cluster's DNS system

* Uses the `POD_IP.NAMESPACE.pod.CLUSTER_DOMAIN` (ex: *192-168-10-10.pod.example.com*) format or `POD_NAME.HEADLESS_SERVICE.NAMESPACE.svc.cluster.local` (ex: *web-1.web-svc.default.svc.cluster.local*)when using headless Services or StatefulSets

<br>

