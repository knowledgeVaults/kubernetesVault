
ACME (Automatic Certificate Management Environment) is the protocol that automates how servers obtain and renew SSL/TLS certificates from Certificate Authorities

## How It Works

1. Client registers with a Certificate Authority (ex: *Certbot*, *Traefik*, *etc.*)
2. Client requests a certificate for their domain
3. Certificate Authority sends a challenge to the client to prove that they own the domain
4. Client completes the challenge to prove their ownership
5. Certificate Authority verifies and issues the certificate which will automatically renew before expiry

## Challenge Types

### HTTP-01

1. The ACME server will send a token to the ACME client 
2. The ACME client will place that token at `http://DOMAIN/.well-known/acme-challenge/TOKEN`
3. The ACME server will perform an HTTP request to that URL
4. Domain validation succeeds if the ACME server gets the correct response

Requirements:
* Port 80 must be open
* Domain must be publicly accessible
* Domain must resolve correctly

### DNS-01

### TLS-ALPN-01

Helps prove that the user controls a domain by responding to a special TLS handshake on port 443

1. The ACME client 
2. The ACME server connects to the client's domain on port 443
3. The ACME server performs a TLS handshake using ALPN