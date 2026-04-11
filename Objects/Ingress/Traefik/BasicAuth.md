
**Basic auth** is the simplest authentication method where the browser sends an `Authorization: Basic` header prompting for a username and password via HTTP basic scheme

* Traefik will validate the credentials against hashed users in a Kubernetes [[Secrets]]
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

## Generating htpasswd Credentials

Traefik's BasicAuth middleware expects `htpasswd`-formatted credentials (apr1 MD5 hash)

```Bash
# Generate hashed password
htpasswd -nb admin mysecretpassword
# admin:$apr1$H6uskkkW$IgXLP6ewTrSuBkTrqE8wj/

# If htpasswd is not installed:
docker run --rm httpd:alpine htpasswd -nb admin password
```

## Creating the Secret

```Bash
# The file must be named 'users'
kubectl create secret generic basic-auth-secret \
  --from-literal=users='admin:$apr1$H6uskkkW$IgXLP6ewTrSuBkTrqE8wj/'
```

Note: Dollar signs in shell must be escaped with single quotes or `\$`.

## Multiple Users

```Bash
# Add multiple users to the same htpasswd file
htpasswd -nb alice password1 > users
htpasswd -nb bob password2 >> users
kubectl create secret generic basic-auth-secret --from-file=users=users
```

## Realm Customisation

```YAML
spec:
  basicAuth:
    secret: basic-auth-secret
    realm: "Restricted Area"    # Text shown in browser login prompt
```

## Commands

```Bash
kubectl get middleware -n NAMESPACE | grep basic
kubectl describe middleware basic-auth
```
