**[[Ingress]] Annotations** are metadata key-value pairs attached to `Ingress` objects that configure Ingress controller-specific behavior beyond the standard Ingress spec

* The standard Ingress spec covers basic host/path routing and TLS termination
* Annotations provide a controller-specific extension mechanism for features like authentication, rate limiting, rewrite rules, and custom timeouts
* Annotation keys are namespaced to the specific controller (e.g., `nginx.ingress.kubernetes.io/`, `traefik.ingress.kubernetes.io/`)

## Common NGINX Ingress Annotations

```YAML
metadata:
  annotations:
    # Rewrite the path before forwarding to the backend
    nginx.ingress.kubernetes.io/rewrite-target: /

    # Enable SSL passthrough (forward TLS directly to pod)
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"

    # Backend connection timeout in seconds
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "60"

    # Enable client certificate authentication
    nginx.ingress.kubernetes.io/auth-tls-secret: "default/ca-secret"
    nginx.ingress.kubernetes.io/auth-tls-verify-client: "on"

    # Enable basic authentication
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: basic-auth-secret
    nginx.ingress.kubernetes.io/auth-realm: "Authentication Required"

    # Rate limiting
    nginx.ingress.kubernetes.io/limit-rps: "10"

    # Force HTTPS redirect
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"

    # CORS
    nginx.ingress.kubernetes.io/enable-cors: "true"
    nginx.ingress.kubernetes.io/cors-allow-origin: "https://app.example.com"
```

## Common Traefik Ingress Annotations

```YAML
metadata:
  annotations:
    traefik.ingress.kubernetes.io/router.entrypoints: websecure
    traefik.ingress.kubernetes.io/router.middlewares: default-redirect-https@kubernetescrd
    traefik.ingress.kubernetes.io/router.tls: "true"
```

## [[Ingress Class]] vs Annotations

* `IngressClass` (via `spec.ingressClassName`) selects which controller handles the Ingress
* Annotations configure **how** the selected controller handles the Ingress
* Prefer `IngressClass` over the deprecated `kubernetes.io/ingress.class` annotation
