The **[[Gateway API]]** is a collection of CRDs that model Kubernetes [[Ingress]] and traffic routing using a role-oriented, expressive API

* Successor to the `Ingress` resource; addresses its limitations around TLS, protocol support, and multi-team workflows
* Implemented as CRDs under the `gateway.networking.k8s.io` API group
* Supported by most major ingress and service mesh controllers (Istio, Traefik, Envoy Gateway, NGINX, etc.)

## Core Resources

| Resource | Scope | Role |
|---|---|---|
| `GatewayClass` | Cluster | Defines a class of Gateways and their controller |
| `Gateway` | Namespaced | Represents a deployed load balancer / proxy instance |
| `HTTPRoute` | Namespaced | Routes HTTP traffic from a Gateway to backend services |
| `TLSRoute` | Namespaced | Routes TLS-passthrough traffic |
| `TCPRoute` | Namespaced | Routes raw TCP traffic |
| `GRPCRoute` | Namespaced | Routes gRPC traffic |

## Role-Oriented Design

Gateway API is designed for multiple personas operating at different trust levels

* **Infrastructure Admin**: manages `GatewayClass` definitions
* **Platform Operator**: manages `Gateway` objects and their listeners
* **Application Developer**: manages `HTTPRoute` and other Route objects attached to Gateways

## Example HTTPRoute

```YAML
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: my-route
  namespace: default
spec:
  parentRefs:
    - name: my-gateway
  hostnames:
    - "app.example.com"
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /api
      backendRefs:
        - name: api-service
          port: 8080
```

## Advantages Over Ingress

* Supports TCP, TLS, UDP, and gRPC routing natively
* Separates concerns across `GatewayClass`, `Gateway`, and Route objects
* Allows cross-namespace routing via `ReferenceGrant` objects
* Rich traffic management: weight-based splitting, header matching, request mirroring

## ReferenceGrant (Cross-Namespace Routing)

```YAML
apiVersion: gateway.networking.k8s.io/v1beta1
kind: ReferenceGrant
metadata:
  name: allow-from-gateway-ns
  namespace: backend
spec:
  from:
    - group: gateway.networking.k8s.io
      kind: HTTPRoute
      namespace: gateway-ns
  to:
    - group: ""
      kind: Service
```

This allows an `HTTPRoute` in `gateway-ns` to reference a `Service` in `backend`.
