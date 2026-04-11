
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

## How Digest Authentication Works

Unlike Basic Auth which sends base64-encoded credentials, Digest Auth uses a challenge-response mechanism

1. Client sends a request without credentials
2. Server responds with `401 Unauthorized` and a `WWW-Authenticate` header containing a nonce
3. Client hashes the credentials with the nonce using MD5 and sends the result
4. Server verifies the hash as credentials are never sent in plaintext

This makes Digest Auth more secure than Basic Auth over unencrypted connections, though HTTPS is still strongly recommended.

## Generating Digest Credentials

```Bash
# Format: username:realm:MD5(username:realm:password)
echo -n "admin:traefik:mysecretpassword" | md5sum
# a2688e031edb4be6a3797f3882655c05

# Create the users file entry
echo "admin:traefik:a2688e031edb4be6a3797f3882655c05" > digestusers

# Create the Kubernetes Secret
kubectl create secret generic digestsecret \
  --from-file=users=digestusers
```

## Testing Digest Auth

```Bash
# curl supports digest auth natively
curl --digest -u admin:mysecretpassword https://app.example.com/
```

## Commands

```Bash
kubectl get middleware -n NAMESPACE | grep digest
kubectl describe middleware digest-auth
```
