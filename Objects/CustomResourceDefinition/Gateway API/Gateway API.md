
* An official Kubernetes project focused on L4 and L7 routing
* Represents the next generation of [[Ingress]], Load Balancing, and Service Mesh APIs
* Requires users to deploy a [[Gateway API]] Controller
* No annotations needed as everything is defined in the `spec` field

## Gateway API Objects

### GatewayClass

* Configured and managed by the Infrastructure Providers
* Defines what the underlying network infrastructure would be

```YAML
apiVersion: gateway.networking.k8s.io/v1
kindL GatewayClass
metadata:
  name: GATEWAY_CLASS_NAME
spec:
  controllerName: example/com/gateway-controller
```

### Gateway

* Configured and managed by the Cluster Operators

```YAML
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: GATEWAY_NAME
spec:
  gatewayClassName: example-class
  listeners:
  - name: http
    protocols: HTTP
    port: 80
```

### HTTPRoute, TCPRoute, GRPCRoute

* Configured and managed by the Application Developers

```YAML
apiVersion: gateway.networking.k8s.io/va
kind: HTTPRoute
metadata:
  name: ROUTE_NAME
spec:
  parentRefs:
  - name: example-gateway
  hostnames:
  - "www.example.com"
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /login
    backendRefs:
    - name: example-svc
      port 8080
```

| Route     | OSI Layer | Routing Discriminator | TLS Support | Purpose |
| --------- | --------- | --------------------- | ----------- | ------- |
| HTTPRoute |           |                       |             |         |
| TLSRoute  |           |                       |             |         |
| TCPRoute  |           |                       |             |         |
| UDPRoute  |           |                       |             |         |
| GRPCRoute |           |                       |             |         |

## Route Table Reference

| Route | OSI Layer | Routing Discriminator | TLS Support | Purpose |
|---|---|---|---|---|
| `HTTPRoute` | L7 | Host, path, headers | Termination | HTTP/HTTPS routing |
| `TLSRoute` | L4 | SNI | Passthrough | TLS passthrough routing |
| `TCPRoute` | L4 | Port | None | TCP traffic routing |
| `UDPRoute` | L4 | Port | None | UDP traffic routing |
| `GRPCRoute` | L7 | Service, method | Termination | gRPC routing |

## Installing Gateway API CRDs

```Bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/latest/download/standard-install.yaml
```

## Advantages Over Ingress

