
**Traefik** is a cloud-native reverse proxy and load balancer designed to work natively with Kubernetes, automatically discovering routing configuration from Kubernetes resources

* Supports both standard Kubernetes [[Ingress]] objects and its own custom CRDs (`IngressRoute`, `Middleware`, `Router`)
* Dynamically reconfigures itself when Kubernetes resources change (No reload required)
* Provides built-in Let's Encrypt integration, middleware chaining, and metrics via Prometheus

## Architecture

```
External Traffic → Traefik EntryPoints
                       │
                   Routers (match rules)
                       │
                  Middlewares (auth, redirect, etc.)
                       │
                   Services (load balance to pods)
```

## EntryPoints

EntryPoints define the ports Traefik listens on

```YAML
# traefik.yaml or Helm values
entryPoints:
  web:
    address: ":80"
  websecure:
    address: ":443"
```

## Kubernetes Ingress Mode

Traefik can be used as a standard Kubernetes Ingress controller

```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    traefik.ingress.kubernetes.io/router.entrypoints: websecure
    traefik.ingress.kubernetes.io/router.tls: "true"
spec:
  ingressClassName: traefik
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```

## Installation via [[Helm]]

```Bash
helm repo add traefik https://traefik.github.io/charts
helm install traefik traefik/traefik --namespace traefik --create-namespace
kubectl port-forward -n traefik svc/traefik-dashboard 9000:9000
```

## Dashboard

Traefik's built-in dashboard shows active routers, services, and middleware at `/dashboard/`.

## Traefik CRD Summary
| CRD | Purpose |
|---|---|
| `IngressRoute` | HTTP/HTTPS routing rules |
| `Middleware` | Authentication, redirects, rate limiting |
| `TLSOption` | TLS settings including mTLS |
| `TLSStore` | Default TLS certificates |
| `TraefikService` | Advanced load balancing (weighted, mirroring) |
