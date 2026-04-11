**Kubernetes DNS** provides automatic service discovery within a cluster by assigning DNS names to Services and (optionally) Pods

* Implemented by **[[CoreDNS]]**, deployed as a [[Deployments]] in the `kube-system` namespace since Kubernetes 1.13
* Every pod has `/etc/resolv.conf` automatically configured by the [[kubelet]] to use the cluster DNS service
* DNS records are created and removed automatically as Services and Pods are created or deleted

## DNS Name Format

### Services

| Scope | DNS Name |
|---|---|
| Same namespace | `SERVICE_NAME` |
| Cross-namespace | `SERVICE_NAME.NAMESPACE` |
| FQDN | `SERVICE_NAME.NAMESPACE.svc.cluster.local` |

### Pods

Pod DNS records are not created by default; they require `spec.hostname` and/or `spec.subdomain`

| Format | DNS Name |
|---|---|
| Pod with hostname + subdomain | `HOSTNAME.SUBDOMAIN.NAMESPACE.svc.cluster.local` |
| Dashed IP (always available) | `10-1-2-3.NAMESPACE.pod.cluster.local` |

## resolv.conf Injected into Every Pod

```
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

`ndots:5` means short names (e.g., `my-service`) are first tried with each search domain suffix before being resolved as absolute names. This allows `curl my-service` to resolve to `my-service.default.svc.cluster.local`.

## Testing DNS Resolution

```Bash
kubectl run dns-test --image=busybox --rm -it --restart=Never -- nslookup kubernetes.default
kubectl run dns-test --image=busybox --rm -it --restart=Never -- nslookup my-service.production.svc.cluster.local
```

## DNS Policies per Pod

```YAML
spec:
  dnsPolicy: ClusterFirst    # Use CoreDNS first (default)
  # Options: Default | ClusterFirst | ClusterFirstWithHostNet | None
```

## Configuring CoreDNS

CoreDNS is configured via the `coredns` ConfigMap in `kube-system`; see [[CoreDNS]] for Corefile syntax and plugin configuration.

## Headless Services and DNS

A headless Service (`ClusterIP: None`) returns the individual pod IPs instead of a single ClusterIP

```YAML
spec:
  clusterIP: None
  selector:
    app: my-statefulset
```

DNS for a headless Service returns all pod IPs as A records, enabling clients to implement their own load balancing or connect to specific pods by name (used by StatefulSets).
