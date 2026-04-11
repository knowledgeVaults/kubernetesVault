## Network Policies

Always define [[Network Policies]] resources to restrict pod-to-pod communication by default

* Start with a default-deny-all [[Ingress]] and egress policy per namespace, then selectively allow required traffic
* Never leave namespaces without any NetworkPolicy because an unprotected namespace allows all ingress by default
* Label pods consistently so selectors in NetworkPolicies are precise and auditable

## DNS and Service Discovery

Use fully qualified service names in configurations to avoid cross-namespace resolution ambiguity

* Prefer `SERVICE.NAMESPACE.svc.cluster.local` in configs where cross-namespace calls are made
* Avoid hardcoding cluster IP addresses; always rely on service DNS names
* Monitor [[CoreDNS]] resource usage and tune replica count based on query rate; CoreDNS is often under-provisioned in large clusters

## Ingress and Load Balancing

Use a dedicated Ingress controller and avoid exposing services as [[NodePort]] or [[LoadBalancer]] unless required

* Consolidate external traffic through a single Ingress controller for easier certificate management and access logging
* Enable TLS termination at the Ingress layer; do not expose plaintext HTTP externally
* Use [[Ingress Class]] resources to explicitly bind Ingress objects to a specific controller in multi-controller environments

## CNI Plugin Selection

Choose a CNI plugin that matches your performance, security, and observability requirements

* **Calico**: best for environments needing NetworkPolicy enforcement at scale with BGP routing
* **Cilium**: eBPF-based; best for high-throughput workloads and advanced observability (Hubble)
* **Flannel**: simple overlay network with minimal overhead; limited NetworkPolicy support
* Avoid mixing CNI plugins in a single cluster

## [[Pod Networking]]

Keep pod CIDR and service CIDR ranges non-overlapping and sized for future growth

* Default pod CIDR (`10.244.0.0/16`) provides 65K IPs so always plan for larger ranges if scaling beyond a few hundred nodes
* Ensure node-to-pod routing is functional across all nodes before deploying workloads
