
Ask
* What's the purpose of this cluster?
* What's the cloud adoption? (Cloud or on-prem?)
* What kind of workloads will be run on the cluster?

Example:
* For education purposes: Minikube or single-node cluster with kubeadm/GCP/AWS
* For development and testing: Multi-node cluster with a Single master and multiple worker nodes set up using kubeadm or on cloud
* For production applications: Highly-available multi-node cluster with multiple master nodes

Storage:
* For high performance: SSD-backed storage
* For multiple concurrent connections: Network-based storage
* Persistent shared volumes for shared access across multiple Pods
* Label nodes with specific disk types
* Use node selectors to assign applications to nodes with specific disk types