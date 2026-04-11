**Kubernetes Extension Points** are the defined interfaces through which external code can extend or modify Kubernetes behavior without modifying the core codebase

* Kubernetes is designed to be composable where most platform capabilities are built via these extension mechanisms
* Extension points follow a plugin model where implementations must conform to a defined interface or protocol

## Categories of Extension Points

### API Extensions

Extend the Kubernetes API surface with new resource types

* **CustomResourceDefinitions (CRDs)**: define new resource types stored in [[etcd]] and served by the [[kube-apiserver]]
* **API Aggregation Layer**: mount a fully external API server under a subpath of the main API server

### Admission Extensions

Intercept and modify or validate API requests before they are persisted

* **MutatingWebhookConfiguration**: external webhook called during the mutation phase; can modify objects
* **ValidatingWebhookConfiguration**: external webhook called during validation; can accept or reject objects
* **ValidatingAdmissionPolicy**: CEL-based policy rules evaluated in-process without an external webhook

### Scheduler Extensions

Influence pod placement decisions

* **Scheduler Profiles**: configure filter, score, and bind plugin chains per profile
* **Custom Schedulers**: deploy a completely separate scheduler binary alongside the default one

### Network Extensions

* **[[Container Network Interface]] (CNI)**: plugins implement [[Pod Networking]] (Calico, Cilium, Flannel)
* **[[Gateway API]] controllers**: implement the `Gateway` and `HTTPRoute` CRDs for advanced [[Ingress]] routing

### Storage Extensions

* **[[Container Storage Interface]] (CSI)**: plugins implement persistent storage provisioning and attachment

### Runtime Extensions

* **[[Container Runtime Interface]] (CRI)**: the [[kubelet]] communicates with container runtimes via this gRPC interface
* **RuntimeClass**: selects which OCI runtime (runc, kata, gVisor) to use for a given pod

### Observability Extensions

* **Custom Metrics Adapter**: exposes external or application metrics to the HPA via the `custom.metrics.k8s.io` API
* **External Metrics API**: exposes metrics from external systems (e.g., a message queue length) for autoscaling

## Extension Pattern: Operator

The **Operator pattern** combines CRDs and a custom controller to automate domain-specific operational tasks

