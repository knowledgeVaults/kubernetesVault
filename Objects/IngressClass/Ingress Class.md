
An `ingressClass` is a cluster-scoped object that tells the cluster which Ingress Controller should handle a given Ingress object

* Defines a logical class of Ingress
* Referenced in the `spec.ingressClassName` field in an Ingress and Kubernetes will try to match that name with the `metadata.name` of the IngressClass

## IngressClass Definition

```YAML
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: OBJECT_NAME
spec:
  controller: CONTROLLER
```

| Controller | spec.controller value         |
| ---------- | ----------------------------- |
| Traefik    | traefik.io/ingress-controller |
| HAProxy    | ingress.k8s.aws/alb           |

