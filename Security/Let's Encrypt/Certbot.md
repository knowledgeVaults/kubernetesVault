
**Certbot** is the official ACME client maintained by the Electronic Frontier Foundation (EFF) for obtaining and renewing Let's Encrypt certificates

* The most widely used standalone ACME client outside of Kubernetes
* In Kubernetes environments, **cert-manager** is generally preferred over Certbot
* Certbot is useful for securing [[Ingress]]/load-balancer nodes directly or for edge servers outside the cluster

## Installation

```Bash
# Ubuntu/Debian
sudo apt install certbot python3-certbot-nginx

# macOS
brew install certbot

# Docker
docker run -it --rm \
  -v /etc/letsencrypt:/etc/letsencrypt \
  -v /var/www/certbot:/var/www/certbot \
  certbot/certbot certonly
```

## Obtaining a Certificate

```Bash
# HTTP-01 challenge (standalone mode — Certbot runs its own web server)
sudo certbot certonly --standalone -d example.com -d www.example.com

# HTTP-01 with Nginx (Certbot configures Nginx automatically)
sudo certbot --nginx -d example.com

# DNS-01 challenge (for wildcards)
sudo certbot certonly --manual --preferred-challenges dns -d "*.example.com"
```

## Certificate Files

Certbot stores certificates under `/etc/letsencrypt/live/DOMAIN/`

| File | Purpose |
|---|---|
| `cert.pem` | Domain certificate only |
| `chain.pem` | Intermediate CA chain |
| `fullchain.pem` | Certificate + chain (use this for TLS) |
| `privkey.pem` | Private key |

## Renewal

```Bash
# Dry-run renewal test
sudo certbot renew --dry-run

# Force immediate renewal
sudo certbot renew --force-renewal

# Automated renewal (cron or systemd timer)
0 0 * * * root certbot renew --quiet
```

## Kubernetes Equivalent

In Kubernetes, cert-manager replaces Certbot by automating certificate lifecycle management as a controller, storing certificates as [[Secrets]] objects.

## Certbot vs cert-manager in Kubernetes

In a Kubernetes cluster, cert-manager is the preferred approach — it manages certificates as Kubernetes objects (`Certificate`, `ClusterIssuer`) and stores them as `Secret` resources natively. Certbot is better suited for standalone servers outside the cluster.
