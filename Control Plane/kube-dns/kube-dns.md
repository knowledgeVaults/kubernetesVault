[[kube-dns]] is the legacy Kubernetes DNS service name that provides cluster-internal DNS resolution for Services and Pods

* The `kube-dns` Service name (in the `kube-system` namespace) is preserved for backward compatibility even in clusters that use [[CoreDNS]]
* Starting with Kubernetes 1.13, **CoreDNS** replaced kube-dns as the default DNS implementation
* All pods have `/etc/resolv.conf` configured to point to the `kube-dns` [[ClusterIP]] for name resolution

## DNS Record Format

Kubernetes DNS follows a predictable naming scheme

| Resource | DNS Name | Example |
|---|---|---|
| Service (same namespace) | `SERVICE_NAME` | `my-service` |
| Service (cross-namespace) | `SERVICE_NAME.NAMESPACE` | `my-service.production` |
| Service (FQDN) | `SERVICE_NAME.NAMESPACE.svc.cluster.local` | `my-service.production.svc.cluster.local` |
| Pod (FQDN) | `POD_IP_DASHED.NAMESPACE.pod.cluster.local` | `10-1-2-3.default.pod.cluster.local` |

## Pod DNS Configuration

Each pod's `/etc/resolv.conf` is injected by the [[kubelet]]

```
nameserver 10.96.0.10          # kube-dns ClusterIP
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

`ndots:5` means names with fewer than 5 dots are tried with each search domain suffix before being resolved as absolute names.

## Custom DNS Configuration per Pod

```YAML
spec:
  dnsPolicy: ClusterFirst        # Use cluster DNS (default)
  dnsConfig:
    nameservers:
      - 8.8.8.8
    searches:
      - my-company.internal
    options:
      - name: ndots
        value: "3"
```

## DNS Policies

| Policy | Behaviour |
|---|---|
| `ClusterFirst` | Cluster DNS first; fall back to upstream (default) |
| `ClusterFirstWithHostNet` | `ClusterFirst` for pods using `hostNetwork: true` |
| `Default` | Inherit node's `/etc/resolv.conf` |
| `None` | Fully custom via `dnsConfig` |

## Debugging DNS

```Bash
# Test DNS resolution from a pod
kubectl run dnstest --image=busybox --rm -it --restart=Never -- nslookup my-service.default.svc.cluster.local

# Check CoreDNS pod status
kubectl get pods -n kube-system -l k8s-app=kube-dns
kubectl logs -n kube-system -l k8s-app=kube-dns
```
