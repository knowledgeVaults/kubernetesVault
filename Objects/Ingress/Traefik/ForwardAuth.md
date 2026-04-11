**ForwardAuth** is a Traefik middleware that delegates authentication to an external HTTP service, allowing any web application to act as the authentication backend

* Traefik forwards the original request headers to the external auth service
* If the auth service returns `2xx`, the request proceeds to the backend
* If the auth service returns `4xx` or `5xx`, the request is rejected (default: `401` or `403` returned to client)

## Middleware Manifest

```YAML
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: forward-auth
  namespace: default
spec:
  forwardAuth:
    address: "http://auth-service.default.svc.cluster.local/verify"
    authResponseHeaders:
      - "X-User-ID"
      - "X-User-Email"
      - "X-User-Roles"
    trustForwardHeader: true
    authRequestHeaders:
      - "Authorization"
      - "Cookie"
```

## Key Fields

| Field | Description |
|---|---|
| `address` | URL of the external auth service |
| `authResponseHeaders` | Headers from auth response to forward to backend |
| `authRequestHeaders` | Headers from original request to include in auth call |
| `trustForwardHeader` | Trust `X-Forwarded-*` headers in the auth request |

## Applying to an IngressRoute

```YAML
spec:
  routes:
    - match: Host(`app.example.com`)
      kind: Rule
      middlewares:
        - name: forward-auth
      services:
        - name: my-app
          port: 80
```

## Auth Service Interface

The auth service receives the original request's headers and must respond with:
* `200 OK` which means it allowed the request; optionally include `X-User-*` headers in the response
* `401 Unauthorized` which means it rejected and returned 401 to the client
* `403 Forbidden` which means it rejected and returned 403 to the client

## Common Backends

* **oauth2-proxy**: OIDC/OAuth2 session management
* **Authelia**: Multi-factor auth with LDAP/TOTP support
* **Authentik**: Self-hosted identity provider with ForwardAuth support

## Error Handling

If the auth service is unreachable, Traefik returns a `500 Internal Server Error` by default. To fail open instead (not recommended for security-sensitive routes):

```YAML
spec:
  forwardAuth:
    address: "http://auth-service/verify"
    # No failurePolicy override available in CE; use failsafe at network level
```
