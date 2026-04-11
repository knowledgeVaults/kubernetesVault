An **[[Ingress Class]]** is a cluster-scoped resource that defines which [[Ingress]] controller handles a given `Ingress` object, allowing multiple controllers to coexist in the same cluster

* Referenced in `Ingress.spec.ingressClassName` because the controller matching that class name processes the Ingress
* Replaced the deprecated `kubernetes.io/ingress.class` annotation-based approach
* One IngressClass can be marked as the cluster default because Ingress objects without an `ingressClassName` use it automatically

## IngressClass Manifest

```YAML
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: nginx
  annotations:
    ingressclass.kubernetes.io/is-default-class: "true"  # Mark as default
spec:
  controller: k8s.io/ingress-nginx
```

## Controller Name Reference

| Controller | spec.controller |
|---|---|
| Ingress-NGINX | `k8s.io/ingress-nginx` |
| Traefik | `traefik.io/ingress-controller` |
| Kong | `ingress-controllers.konghq.com/kong` |
| AWS ALB | `ingress.k8s.aws/alb` |

## Default IngressClass

When marked as default, all Ingress objects that omit `spec.ingressClassName` are handled by this class

```Bash
kubectl get ingressclass
# NAME    CONTROLLER             PARAMETERS   AGE
# nginx   k8s.io/ingress-nginx   <none>       5d   (default)
```

## Multiple Ingress Controllers

Running multiple controllers enables different routing capabilities for different workloads

```YAML
# Public traffic → Traefik with Let's Encrypt
spec:
  ingressClassName: traefik

# Internal traffic → NGINX with basic auth
spec:
  ingressClassName: nginx-internal
```

## Commands

```Bash
kubectl get ingressclass
kubectl describe ingressclass nginx
```

## Removing the Default IngressClass

```Bash
kubectl annotate ingressclass nginx \
  ingressclass.kubernetes.io/is-default-class-
```

This removes the default annotation. Unset it when migrating to a different default controller to prevent Ingress objects from being claimed by the wrong controller.

## Parameters (Controller-Specific Config)

```YAML
spec:
  controller: ingress.k8s.aws/alb
  parameters:
    apiGroup: elbv2.k8s.aws
    kind: IngressClassParams
    name: my-alb-params
```

Some controllers support an `IngressClassParams` CRD for additional configuration (e.g., AWS ALB scheme, target type).
