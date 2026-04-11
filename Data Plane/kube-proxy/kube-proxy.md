[[kube-proxy]] is a network proxy that runs on every node in a Kubernetes cluster, maintaining network rules that route traffic destined for Service ClusterIPs to the correct backend pods

* Watches the [[kube-apiserver]] for `Service` and [[Endpoint Slices]] (or `Endpoints`) objects
* Programs host-level networking rules so that any traffic targeting a Service IP is transparently forwarded to a healthy pod
* Runs as a [[Daemon Sets]] in most cluster distributions

## Operating Modes

### iptables (Default)

kube-proxy creates `iptables` DNAT rules for each Service and its backends

* Efficient for small-to-medium clusters; rule evaluation is O(n) — degrades at 10,000+ services
* Load balancing is random across all endpoints (equal-weight, stateless)

### IPVS

Uses the Linux IPVS kernel module for load balancing

```Bash
kube-proxy --proxy-mode=ipvs
```

* O(1) lookup instead of O(n) iptables chain traversal — much faster at scale
* Supports multiple load balancing algorithms: `rr` (round-robin), `lc` (least connections), `sh` (source hash)
* Requires the `ip_vs` kernel module; `ipset` must be installed

### userspace (Legacy)

Routes traffic through a userspace proxy process — not recommended; high latency and resource overhead.

## How kube-proxy Routes Service Traffic

For a [[ClusterIP]] Service on `10.96.0.1:80` backed by pods at `10.1.0.5:8080` and `10.1.0.6:8080`:

```
iptables -t nat -A KUBE-SERVICES -d 10.96.0.1/32 -p tcp --dport 80 -j KUBE-SVC-XXXXX
iptables -t nat -A KUBE-SVC-XXXXX -j KUBE-SEP-POD1  (50% probability)
iptables -t nat -A KUBE-SVC-XXXXX -j KUBE-SEP-POD2
iptables -t nat -A KUBE-SEP-POD1  -j DNAT --to-destination 10.1.0.5:8080
iptables -t nat -A KUBE-SEP-POD2  -j DNAT --to-destination 10.1.0.6:8080
```

## Checking kube-proxy Mode

```Bash
kubectl get configmap kube-proxy -n kube-system -o yaml | grep mode
```

## View Service IP Rules

```Bash
iptables -t nat -L KUBE-SERVICES -n | grep SERVICE_NAME
ipvsadm -Ln   # If using IPVS mode
```

## kube-proxy vs CNI Plugin

* **kube-proxy**: handles Service-level routing (ClusterIP --> pod IP); it does NOT route pod-to-pod traffic
* **CNI plugin**: handles pod-to-pod routing directly between nodes (overlay networks, BGP routes)
