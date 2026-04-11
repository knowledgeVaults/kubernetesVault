# Kubernetes Knowledge Vault

A structured Obsidian knowledge vault documenting Kubernetes architecture, objects, operations, and best practices.

## Vault Structure

```
├── Architecture/          Control plane and container runtime internals
├── Best Practices/        Networking, security, availability, IaC
├── Cluster Mainetance/    Upgrades, backups, OS maintenance
├── Container Network Interface.md
├── Container Runtime Interface.md
├── Container Storage Interface.md
├── Control Plane/         kube-apiserver, etcd, scheduler, controller-manager
├── Data Plane/            kubelet, kube-proxy, cAdvisor
├── Extension Points/      Kubernetes extensibility mechanisms
├── Extensions/            CNI, CSI, CRI implementations
├── JSONPath/              kubectl JSONPath query reference
├── Networking/            DNS, pod networking, services, CNI
├── Objects/               All Kubernetes resource types
│   ├── Pods/              Pod spec, init containers, probes
│   ├── Deployments/       Rollouts, strategies, lifecycle
│   ├── Services/          ClusterIP, NodePort, LoadBalancer
│   ├── Ingress/           Ingress, IngressClass, Traefik
│   ├── Storage/           PV, PVC, StorageClass
│   ├── RBAC/              Roles, ClusterRoles, Bindings
│   └── ...
├── Observability/         Metrics, logs, tracing
├── Package Managers/      Helm, Kustomize
├── Pipelines/             Argo CD, GitOps
├── Security/              TLS, image security, RBAC, Let's Encrypt
├── Tools/                 kubeadm, minikube, kubectx, kubens
└── Troubleshooting/       Nodes, pods, networking, Calico
```

## Key Concepts Index

* **Core Architecture**: [[Control Plane]], [[Data Plane]], [[etcd]], [[kube-apiserver]]
* **Workloads**: [[Pods]], [[Deployments]], [[StatefulSets]], [[DaemonSets]]
* **Networking**: [[Services]], [[Ingress]], [[Network Policies]], [[CoreDNS]]
* **Storage**: [[PersistentVolumes]], [[PersistentVolumeClaims]], [[StorageClasses]]
* **Security**: [[RBAC]], [[Security Contexts]], [[Secrets]]
* **Operations**: [[Cluster Upgrades]], [[Backups]], [[Taints and Tolerations]]
* **GitOps**: [[Argo CD]], [[Helm]]

## Prerequisites

* Basic Linux command-line proficiency
* Familiarity with containers and Docker
* Understanding of networking fundamentals (TCP/IP, DNS)

