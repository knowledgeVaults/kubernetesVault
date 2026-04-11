`IngressRoute` is Traefik's custom CRD for defining HTTP and HTTPS routing rules with full access to Traefik features not available in the standard Kubernetes [[Ingress]] spec

* More expressive than Kubernetes `Ingress` as it supports path regex, headers, query parameters, and TCP/TLS routes
* Requires Traefik v2+ with CRDs installed
* Middleware can be applied per route for auth, redirects, rate limiting, etc.

## Basic IngressRoute

```YAML
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: my-app-route
  namespace: default
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`app.example.com`) && PathPrefix(`/api`)
      kind: Rule
      services:
        - name: api-service
          port: 8080
      middlewares:
        - name: auth-middleware
    - match: Host(`app.example.com`) && PathPrefix(`/`)
      kind: Rule
      services:
        - name: frontend-service
          port: 80
  tls:
    certResolver: letsencrypt
```

## Route Matching Rules

| Rule | Example |
|---|---|
| `Host()` | `Host("app.example.com")` |
| `PathPrefix()` | `PathPrefix("/api")` |
| `Path()` | `Path("/health")` |
| `Method()` | `Method("GET", "POST")` |
| `Header()` | `Header("X-Custom-Header", "value")` |
| `Query()` | `Query("debug", "true")` |

Rules can be combined with `&&` (AND) and `\|\|` (OR).

## TLS Configuration

```YAML
tls:
  secretName: my-tls-secret    # Use a specific Secret
  # OR
  certResolver: letsencrypt    # Use an ACME resolver
```

## Commands

```Bash
kubectl get ingressroute -n NAMESPACE
kubectl describe ingressroute my-app-route
```

## Combining with Standard Ingress

IngressRoute and standard Kubernetes Ingress can coexist in the same cluster. The IngressRoute CRD provides Traefik-specific features, while Ingress provides portability across controllers.
```Bash
kubectl get ingressroute -A
kubectl get ingress -A
```

## Debugging IngressRoute

```Bash
kubectl describe ingressroute my-app-route -n default
# Check Traefik dashboard at /dashboard/ for active routers and any errors
kubectl logs -n traefik deploy/traefik | grep -i error
```
