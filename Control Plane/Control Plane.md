
The control plane is the set of components that manage the overall state and behavior of a cluster

* Serves as the management layer of Kubernetes that orchestrates the cluster and worker nodes

## Core Components

| Component                    | Purpose                                                                                                                                                 |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [[kube-apiserver]]           | The front-end of the control plane that exposes the Kubernetes API and serves as the primary entry point for all administrative and user operations     |
| [[etcd]]                     | A distributed key-value store that holds all cluster data                                                                                               |
| [[kube-scheduler]]           | Watches for newly created pods without assigned nodes and binds them to suitable worker nodes based on resource requirements, constraints, and policies |
| [[kube-controller-manager]]  | Runs various controllers that monitor the cluster's state via the API server and reconcile differences between the cluster's current and desired state  |
| [[cloud-controller-manager]] |                                                                                                                                                         |
| [[kube-dns]]                 |                                                                                                                                                         |
