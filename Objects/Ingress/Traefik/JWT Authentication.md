**JWT Authentication** in Traefik validates JSON Web Tokens on incoming requests before forwarding them to backend services

* Implemented via the **ForwardAuth** middleware by delegating token validation to an external auth service
* Alternatively, Traefik Enterprise offers a built-in JWT middleware
* The typical OSS approach uses ForwardAuth pointing to a JWT validation service (e.g., Authelia, oauth2-proxy)

## JWT Validation via ForwardAuth

```YAML
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: jwt-auth
  namespace: default
spec:
  forwardAuth:
    address: "http://jwt-validator.default.svc.cluster.local/verify"
    authResponseHeaders:
      - "X-User-ID"
      - "X-User-Email"
      - "X-User-Roles"
    trustForwardHeader: true
```

## IngressRoute with JWT Middleware

```YAML
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: protected-route
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`api.example.com`)
      kind: Rule
      middlewares:
        - name: jwt-auth
      services:
        - name: api-service
          port: 8080
  tls:
    certResolver: letsencrypt
```

## JWT Validator Service

The ForwardAuth address points to a service that validates the Bearer token

* Receives the original request headers including `Authorization: Bearer TOKEN`
* Returns `200 OK` if valid (Traefik forwards the request)
* Returns `401 Unauthorized` if invalid (Traefik blocks the request)
* Can return user info headers (`X-User-ID`, etc.) which are forwarded to the backend

## oauth2-proxy Example

`oauth2-proxy` is commonly used as the ForwardAuth backend for OIDC/JWT validation

```Bash
helm repo add oauth2-proxy https://oauth2-proxy.github.io/manifests
helm install oauth2-proxy oauth2-proxy/oauth2-proxy \
  --set config.clientID=MY_CLIENT_ID \
  --set config.clientSecret=MY_SECRET \
  --set config.cookieSecret=$(openssl rand -base64 32)
```

## Verifying JWT Token Claims

The ForwardAuth service receives the full request including the `Authorization` header and can validate specific JWT claims

```Python
# Example JWT validation service (Python/Flask)
from flask import request, jsonify
import jwt

@app.route('/verify')
def verify():
    token = request.headers.get('Authorization', '').replace('Bearer ', '')
    try:
        payload = jwt.decode(token, PUBLIC_KEY, algorithms=['RS256'])
        return '', 200, {
            'X-User-ID': payload['sub'],
