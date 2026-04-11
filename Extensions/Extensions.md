**Kubernetes Extensions** are components built on top of Kubernetes extension points that add functionality not present in the core distribution

* Extensions are distributed as separate projects and installed independently into a cluster
* They follow the extension interfaces (CRI, CNI, CSI, CRD + controller, webhooks) rather than modifying Kubernetes itself
* The CNCF ecosystem hosts hundreds of graduated and sandbox extension projects

## Categories

### [[Container Runtime]] Extensions (CRI)

Kubernetes supports multiple container runtimes via the CRI interface

* [[containerd]]: default runtime in most distributions
* `CRI-O`: purpose-built CRI runtime for Kubernetes, used in OpenShift
* `kata-containers`: hardware-virtualized containers for stronger isolation

### Network Extensions (CNI)

CNI plugins implement [[Pod Networking]] and optional [[Network Policies]] enforcement

* `Calico`: BGP-based networking with strong NetworkPolicy support
* `Cilium`: eBPF-based; advanced observability and L7 policies via Hubble
* `Flannel`: simple VXLAN overlay; limited policy support
* `Weave Net`: mesh networking with built-in encryption

### Storage Extensions (CSI)

CSI drivers provision, attach, and mount persistent storage

* `aws-ebs-csi-driver`, `gcp-pd-csi-driver`, `azure-disk-csi-driver` for cloud block storage
* `nfs-subdir-external-provisioner` for NFS-backed PVCs
* `rook-ceph` for cloud-native Ceph storage within Kubernetes

### [[Ingress]] and Traffic Extensions

* `Ingress-NGINX`: most widely used HTTP reverse proxy Ingress controller
* `Traefik`: dynamic configuration-aware Ingress controller
* `Envoy Gateway`: [[Gateway API]] implementation backed by Envoy proxy
* `Istio` / `Linkerd`: service mesh for mTLS, traffic management, and observability

### Autoscaling Extensions

* `metrics-server`: resource metrics pipeline for HPA and `kubectl top`
* `KEDA`: event-driven autoscaling using external event sources

### GitOps and CI/CD Extensions

* [[Argo CD]]`: GitOps continuous delivery controller
* `Flux`: GitOps operator with [[Helm]] and [[Kustomize]] integration
