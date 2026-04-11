**Traefik Middlewares** are pluggable HTTP processing units that transform requests or responses before they reach the backend service

* Defined as CRD objects (`Middleware` in the `traefik.io/v1alpha1` API group)
* Applied to routers via the `middlewares` field in an `IngressRoute` or via [[Ingress]] annotations
* Multiple middlewares can be chained since they execute in the order listed

## Middleware Manifest

```YAML
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: my-middleware
  namespace: default
spec:
  # One middleware type per object
```

## Common Middleware Types

### Authentication

```YAML
spec:
  basicAuth:
    secret: my-auth-secret          # htpasswd format users
  # or
  digestAuth:
    secret: my-digest-secret
  # or
  forwardAuth:
    address: "http://auth-svc/verify"
    authResponseHeaders: ["X-User"]
```

### Redirects

```YAML
spec:
  redirectScheme:
    scheme: https
    permanent: true   # 301 vs 302 redirect
```

### Headers

```YAML
spec:
  headers:
    customRequestHeaders:
      X-Custom-Header: "value"
    customResponseHeaders:
      X-Frame-Options: "DENY"
    sslRedirect: true
    stsSeconds: 31536000
```

### Rate Limiting

```YAML
spec:
  rateLimit:
    average: 100
    burst: 200
    period: 1s
    sourceCriterion:
      ipStrategy:
        depth: 1
```

### Strip Prefix

```YAML
spec:
  stripPrefix:
    prefixes:
      - /api
```

## Applying a Middleware to an IngressRoute

```YAML
spec:
  routes:
    - match: Host(`app.example.com`)
      middlewares:
        - name: my-middleware
        - name: redirect-https
      services:
        - name: backend-svc
          port: 80
```

## Commands

```Bash
kubectl get middleware -n NAMESPACE
kubectl describe middleware my-middleware
```

## Middleware Scoping

Middlewares can be cross-namespace by using the `NAMESPACE-NAME@kubernetescrd` syntax in IngressRoute references:

```YAML
middlewares:
  - name: auth-middleware@default    # Middleware 'auth-middleware' in 'default' namespace
```
