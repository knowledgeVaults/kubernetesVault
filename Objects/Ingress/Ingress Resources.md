**[[Ingress]] Resources** are the Kubernetes objects that define routing rules for HTTP and HTTPS traffic entering the cluster via an Ingress controller

* A set of rules applied on the Ingress controller that maps hostnames and URL paths to backend Services
* Must be paired with a running `IngressController` because Ingress objects alone do nothing without one
* Support path-based and host-based routing; can terminate TLS

## Ingress Manifest

```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx    # Select which controller handles this Ingress
  tls:
    - hosts:
        - app.example.com
      secretName: app-tls-secret    # TLS cert stored as a Secret
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: api-service
                port:
                  number: 8080
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-service
                port:
                  number: 80
```

## Path Types

| PathType | Behaviour |
|---|---|
| `Prefix` | Matches any path starting with the given prefix |
| `Exact` | Matches the path exactly |
| `ImplementationSpecific` | Matching is controller-specific |

## Host-Based Routing

```YAML
rules:
  - host: api.example.com
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: api-service
              port:
                number: 80
  - host: www.example.com
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: web-service
              port:
                number: 80
```

## Commands

```Bash
kubectl get ingress [-n NAMESPACE]
kubectl describe ingress my-ingress
```
