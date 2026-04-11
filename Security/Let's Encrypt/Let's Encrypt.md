
**Let's Encrypt** is a free, automated, and open Certificate Authority (CA) that provides Domain Validated (DV) TLS/SSL certificates via the ACME protocol

* Certificates are valid for 90 days and must be renewed automatically
* Provides a free alternative to commercial CAs for public HTTPS
* Widely used in Kubernetes via **cert-manager** which automates the issuance and renewal process

## How It Works

1. A domain owner runs an ACME client (cert-manager, Certbot, Traefik) on their server
2. The client contacts the Let's Encrypt ACME server and requests a certificate for the domain
3. Let's Encrypt issues a **challenge** to prove domain ownership (HTTP-01, DNS-01, or TLS-ALPN-01)
4. The client completes the challenge by placing a token at a well-known URL or in a DNS TXT record
5. Let's Encrypt verifies the challenge and issues the certificate
6. The certificate is stored as a Kubernetes `[[Secrets]]` by cert-manager

## Integration with Kubernetes (cert-manager)

cert-manager is the standard tool for automating Let's Encrypt certificate management in Kubernetes

```Bash
helm repo add jetstack https://charts.jetstack.io
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager --create-namespace \
  --set crds.enabled=true
```

## ClusterIssuer for Let's Encrypt

```YAML
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: admin@example.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
      - http01:
          ingress:
            class: nginx
```

## Certificate Request

```YAML
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: my-tls
  namespace: default
spec:
  secretName: my-tls-secret
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
  dnsNames:
    - app.example.com
```

## Rate Limits

Let's Encrypt enforces rate limits on the production endpoint. Use the **staging** endpoint during testing to avoid hitting limits. The most common limit is 5 duplicate certificates per week per registered domain.
