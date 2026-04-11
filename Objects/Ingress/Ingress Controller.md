An **[[Ingress]] Controller** is the component that reads `Ingress` objects and implements the routing rules by configuring a reverse proxy or load balancer running inside the cluster

* Kubernetes does not ship with a default Ingress controller — one must be explicitly deployed
* Ingress controllers run as Deployments (or DaemonSets) and expose themselves via a `Service` (usually [[LoadBalancer]] or [[NodePort]])
* Each Ingress controller registers one or more [[Ingress Class]] objects; Ingress resources reference a class via `spec.ingressClassName`

## How It Works

```
External Traffic → Service (LoadBalancer) → Ingress Controller Pod
                                                  │
                                     Watches Ingress objects via API server
                                                  │
                                     Configures internal proxy rules
                                                  │
                                     Routes to backend Services
```

## Common Ingress Controllers

| Controller | Description |
|---|---|
| **Ingress-NGINX** | Most widely used; nginx-based; rich annotation support |
| **Traefik** | Dynamic config; CRD-native; built-in Let's Encrypt |
| **HAProxy** | High-performance; used in OpenShift |
| **AWS ALB Controller** | Provisions AWS Application Load Balancers |
| **Kong** | API gateway with plugin ecosystem |

## Deploying Ingress-NGINX

```Bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

## Required Resources

An Ingress controller [[Deployments]] requires:
* A `Deployment` or [[Daemon Sets]] running the proxy process
* A `Service` (typically `LoadBalancer`) exposing ports 80 and 443
* A [[Service Accounts]] with [[RBAC]] permissions to watch Ingress, Service, and Endpoints objects
* An `IngressClass` CRD resource identifying it as the handler for a class name

## Commands

```Bash
kubectl get pods -n ingress-nginx
kubectl get ingressclass
kubectl get svc -n ingress-nginx    # Get external IP/port
```
