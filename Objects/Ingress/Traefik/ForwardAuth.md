
ForwardAuth is an authentication method that proxies authentication to an external service

* Grants access on 2xx response
* Traefik forwards request details via `X-Forwared-*` headers and copies specified response headers

## Middleware

```YAML
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: forward-auth
spec:
  forwardAuth:
    address: "http://auth-service.default.svc.cluster.local/verify"
    authResponseHeaders:
      - "X-User"
    trustForwardHeader: true
```

## IngressRoute