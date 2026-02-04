# Kubernetes: Ingress

An Ingress is an API object that defines how external HTTP/HTTPS traffic should be routed to services inside a Kubernetes cluster

* Acts as a Layer 7 entry point that routes traffic based on rules to different backend services
* Exposes internal services over HTTP/HTTPS without giving each service its own external load balancer or NodePort
* Provides basic load-balancing across pods behind the target Service

# Ingress spec Fields

```YAML
spec:
  ingressClassName: nginx-example   # Specify which Ingress Controller should handle this Ingress
  rules:                            # Specify the routing rules for incoming HTTP requests
  - http:                           # Define HTTP-based routing rules
      paths:                        # Define the URL path matching rules
      - path: /testpath             # List the URL path that the Ingress will match
        pathType: Prefix            # Define the type of matching to use
        backend:                    # Define where matching traffic should be forwarded to 
          service:                  
            name: test              
            port:
              number: 80
```

<br>

## Ingress Controllers

| Ingress Controller | ingressClassName | Description |
| --- | --- | --- |
| NGINX Ingress Controller | `nginx` | For general-purpose use |
| NGINX Inc. Ingress | `nginx-CUSTOM` | For advanced NGINX features |
| Traefik | `traefik` | For dynamic configs (Docker and Kubernetes friendly) |
| HAProxy Ingress | `haproxy` | For high performance and fine-grained control |
| Istio | `istio` | For service mesh, mTLS, and traffic shaping |
| Kong | `kong` | For API gateways and plugins with rate-limit
| Contour | `contour` | Envoy-based |

<br>

## Path Types

| Path Type | Description |
| --- | --- |
| `Prefix` | Matches all the paths that start with the specified path |
| `Exact` | Matches all the paths that match exactly the specified path |
| `ImplementationSpecific` | The Ingress Controller will decide how to interpret the path |