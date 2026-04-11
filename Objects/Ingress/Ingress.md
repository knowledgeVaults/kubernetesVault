An **[[Ingress]]** is a Kubernetes API object that manages external HTTP and HTTPS access to Services within a cluster, providing host-based and path-based routing, TLS termination, and load balancing

* Requires an **Ingress Controller** (NGINX, Traefik, HAProxy, etc.) to be deployed — the Ingress object alone does nothing
* Acts as an L7 (application layer) router — smarter than a [[NodePort]] or [[LoadBalancer]] Service for HTTP traffic
* A single Ingress can consolidate routing for multiple services behind one external IP

## Ingress vs Service

| Feature | Service (LoadBalancer/NodePort) | Ingress |
|---|---|---|
| Layer | L4 (TCP/UDP) | L7 (HTTP/HTTPS) |
| SSL termination | No | Yes |
| Path-based routing | No | Yes |
| Host-based routing | No | Yes |
| Cost | One LB per service | One LB for all services |

## Ingress Manifest

```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  namespace: production
spec:
  ingressClassName: nginx
  tls:
    - hosts:
        - app.example.com
      secretName: tls-secret
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: api-service
                port:
                  number: 8080
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend
                port:
                  number: 80
```

## Limitations of Standard Ingress

* No support for TCP/UDP routing (HTTP/HTTPS only)
* No built-in traffic weighting or canary routing
* Multi-tenant access control is difficult
* Annotation-based configuration is controller-specific and not portable

These limitations led to the **[[Gateway API]]** as the next-generation replacement.

## Commands

```Bash
kubectl get ingress [-n NAMESPACE]
kubectl describe ingress my-ingress
```

## Ingress TLS [[Secrets]]

The TLS Secret must contain `tls.crt` and `tls.key` fields:

```Bash
kubectl create secret tls tls-secret \
  --cert=path/to/tls.crt \
  --key=path/to/tls.key \
  -n NAMESPACE
```

cert-manager can provision and rotate these Secrets automatically.
