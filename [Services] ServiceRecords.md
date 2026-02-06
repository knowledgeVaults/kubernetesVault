# Kubernetes: Service Records

A service record refers to the DNS record that is created for a Service object within a Kubernetes cluster

* Each pod has it's own unique service name that's DNS-resolvable and follows the `SERVICE_NAME.NAMESPACE.svc.cluster.local` pattern
* Pods can only reach services using its service name is both are in the same namespace