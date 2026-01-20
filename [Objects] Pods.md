# Kubernetes: Pod

<r>

# Service Name

* Each pod has it's own unique service name that's DNS-resolvable and in the format of `SERVICE_NAME.NAMESPACE.svc.cluster.local`
* Pods can only reach services using its service name is both are in the same namespace
