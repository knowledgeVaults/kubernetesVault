A **[[ClusterIP]]** Service is the default Service type in Kubernetes, exposing a virtual IP accessible only from within the cluster

* The ClusterIP is allocated from the `--service-cluster-ip-range` configured on the [[kube-apiserver]] (default `10.96.0.0/12`)
* Traffic to the ClusterIP is load-balanced across healthy pods matching `spec.selector` via iptables/IPVS rules on each node
* Cannot be reached from outside the cluster without additional mechanisms ([[Ingress]], [[NodePort]], [[LoadBalancer]], or `kubectl port-forward`)

## Manifest

```YAML
apiVersion: v1
kind: Service
metadata:
  name: my-clusterip-service
  namespace: default
spec:
  type: ClusterIP       # Default — can be omitted
  selector:
    app: my-app
  ports:
    - name: http
      protocol: TCP
      port: 80          # Service port
      targetPort: 8080  # Container port
```

## DNS Resolution

The cluster DNS ([[CoreDNS]]) automatically creates an A record for every ClusterIP Service

```
my-clusterip-service.default.svc.cluster.local → 10.96.x.x
```

Pods can reach the service using any of these forms:
* `my-clusterip-service` (same namespace)
* `my-clusterip-service.default`
* `my-clusterip-service.default.svc.cluster.local` (FQDN)

## Inspecting a ClusterIP Service

```Bash
kubectl get svc my-clusterip-service
kubectl describe svc my-clusterip-service
kubectl get endpoints my-clusterip-service
```

## Session Affinity

By default, [[kube-proxy]] randomly selects a backend pod per connection. To sticky-route traffic from the same client IP:

```YAML
spec:
  sessionAffinity: ClientIP
  sessionAffinityConfig:
    clientIP:
      timeoutSeconds: 10800    # 3 hours
```

## Headless ClusterIP (clusterIP: None)

Returns individual Pod IPs via DNS instead of a single virtual IP since it's used by StatefulSets for stable per-Pod DNS names.

## kube-proxy Mode Impact

In `iptables` mode, load balancing is random per new connection. In `ipvs` mode, multiple algorithms are available (round-robin, least-connections, source-hash). The IPVS mode is more efficient at scale (>1000 services).
