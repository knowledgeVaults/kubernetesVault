
DigestAuth is an authentication method that uses HTTP Digest scheme (challenge-response authentication) 

* Improves security by hashing credentials instead of sending them base64-encoded in plain text

## Middleware

```YAML
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: digest-auth
spec:
  digestAuth:
    secret: digestsecret
---
apiVersion: v1
kind: Secret
metadata:
  name: digestsecret
data:
  users: |
    dGVzdDp0cmFlZmlrOmEyNjg4ZTAzMWVkYjRiZTZhMzc5N2YzODgyNjU1YzA1 # test:traefik:a2688e031edb4be6a3797f3882655c05

```

## IngressRoute

```YAML
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: secure-route
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`example.com`)
      kind: Rule
      middlewares:
        - name: basic-auth
      services:
        - name: whoami
          port: 80
```