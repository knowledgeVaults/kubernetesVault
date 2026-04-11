
**Mutual TLS (mTLS)** in Traefik requires both the server and the client to present valid TLS certificates, providing two-way authentication

* Ensures that only clients with a certificate signed by a trusted CA can access the backend
* Useful for API security, service-to-service authentication, and zero-trust networking
* Configured via Traefik's `TLSOption` CRD

## TLSOption CRD

```YAML
apiVersion: traefik.io/v1alpha1
kind: TLSOption
metadata:
  name: mtls-option
  namespace: default
spec:
  minVersion: VersionTLS12
  clientAuth:
    secretNames:
      - client-ca-secret    # Kubernetes Secret containing the CA cert
    clientAuthType: RequireAndVerifyClientCert
```

## Client CA [[Secrets]]

Create a Kubernetes Secret containing the CA certificate that signed the client certificates

```Bash
kubectl create secret generic client-ca-secret \
  --from-file=tls.ca=./client-ca.crt \
  -n default
```

## IngressRoute with mTLS

```YAML
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: mtls-route
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`secure-api.example.com`)
      kind: Rule
      services:
        - name: api-service
          port: 8080
  tls:
    secretName: server-tls-secret    # Server's TLS certificate
    options:
      name: mtls-option              # Reference to TLSOption requiring client cert
```

## Generating Client Certificates

```Bash
# Generate client key and CSR
openssl genrsa -out client.key 2048
openssl req -new -key client.key -out client.csr -subj "/CN=my-client"

# Sign with CA
openssl x509 -req -in client.csr -CA client-ca.crt -CAkey client-ca.key \
  -CAcreateserial -out client.crt -days 365
```

## Testing mTLS

```Bash
curl --cert client.crt --key client.key --cacert server-ca.crt \
  https://secure-api.example.com/health
```

## Verifying Client Certificate

```Bash
# Check which cert the client is presenting
openssl s_client -connect secure-api.example.com:443 \
  -cert client.crt -key client.key -CAfile server-ca.crt
```

A `Verify return code: 0 (ok)` means the handshake succeeded.
