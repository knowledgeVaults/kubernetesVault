
**Basic auth** is the simplest authentication method where the browser sends an `Authorization: Basic` header prompting for a username and password via HTTP basic scheme

* Traefik will validate the credentials against hashed users in a Kubernetes Secret
* Implemented via the **BasicAuth Middleware**
* Passwords are generate with `htpasswd` and **base64-encoded** in the Secret's `users` key

## Middleware

```YAML
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: basic-auth
spec:
  basicAuth:
    secret: authsecret
---
apiVersion: v1
kind: Secret
metadata:
  name: authsecret
data:
  users: |
    dGVzdDokYXByMSRINnVza2trVyRJZ1hMUDZld1RyU3VCa1RycUU4d2ov # test:$apr1$H6uskkkW$IgXLP6ewTrSuBkTrqE8wj/

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
