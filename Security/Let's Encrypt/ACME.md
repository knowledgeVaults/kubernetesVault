
**ACME (Automatic Certificate Management Environment)** is the protocol that automates the issuance and renewal of TLS certificates between a client and a Certificate Authority

* Standardised as RFC 8555
* Used by Let's Encrypt, ZeroSSL, and other CAs
* Implemented by cert-manager, Certbot, Traefik, and other tools

## Protocol Flow

1. **Account Registration**: client generates a key pair and registers with the CA's ACME endpoint
2. **Order Creation**: client submits an order specifying the domain names to certify
3. **Challenge Authorisation**: CA issues one or more challenges per domain to prove control
4. **Challenge Completion**: client responds to the challenge
5. **Order Finalisation**: CA verifies challenges and issues the certificate
6. **Certificate Download**: client retrieves and stores the certificate

## Challenge Types

### HTTP-01

Proves domain ownership by serving a token at `http://DOMAIN/.well-known/acme-challenge/TOKEN`

* Requires port 80 to be publicly accessible
* Simple to configure; supported by virtually all ACME clients
* Cannot be used for wildcard certificates

### DNS-01

Proves ownership by creating a `_acme-challenge.DOMAIN` TXT DNS record

* Works even without a publicly reachable HTTP server
* Supports wildcard certificates (`*.example.com`)
* Requires DNS provider API access for automation

### TLS-ALPN-01

Proves ownership via a special TLS handshake on port 443 using the ALPN extension

* Port 443 must be publicly accessible
* Less commonly implemented than HTTP-01 and DNS-01

## ACME Servers

| CA | ACME Endpoint |
|---|---|
| Let's Encrypt (prod) | `https://acme-v02.api.letsencrypt.org/directory` |
| Let's Encrypt (staging) | `https://acme-staging-v02.api.letsencrypt.org/directory` |
| ZeroSSL | `https://acme.zerossl.com/v2/DV90` |

Always use the staging endpoint during development to avoid hitting Let's Encrypt rate limits.

## Rate Limits

Let's Encrypt limits issuance to 50 new certificates per registered domain per week. Always develop against the staging endpoint (`acme-staging-v02.api.letsencrypt.org`) and switch to production only when ready.
