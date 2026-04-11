**EndpointSlices** are the scalable successor to `Endpoints` objects, representing subsets of the network endpoints backing a Kubernetes Service

* Introduced in Kubernetes 1.17 (GA in 1.21) to address scalability issues with the original `Endpoints` resource
* Each [[Endpoint Slices]] holds up to 100 endpoints by default, preventing the megaobject problem of a single Endpoints resource for services with thousands of pods
* Consumed by [[kube-proxy]] (and CNI plugins) to program routing rules on each node

## Why EndpointSlices

The original `Endpoints` object stored all pod IPs for a Service in a single object

* A Service with 1000 pods had a single `Endpoints` object ~100KB in size
* Every pod addition or removal triggered a full rewrite and broadcast of this large object to all nodes
* EndpointSlices shard endpoints across multiple small objects, so changes only update one slice

## EndpointSlice Structure

```YAML
apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: my-service-abc12
  labels:
    kubernetes.io/service-name: my-service
addressType: IPv4
ports:
  - name: http
    protocol: TCP
    port: 8080
endpoints:
  - addresses:
      - "10.1.2.3"
    conditions:
      ready: true
      serving: true
      terminating: false
    targetRef:
      kind: Pod
      name: my-pod-xyz
      namespace: default
```

## Key Fields

| Field | Description |
|---|---|
| `addressType` | `IPv4`, `IPv6`, or `FQDN` |
| `endpoints[].conditions.ready` | Pod has passed its readiness probe |
| `endpoints[].conditions.serving` | Endpoint can receive traffic (even if terminating) |
| `endpoints[].conditions.terminating` | Pod is in the process of terminating |

## Inspecting EndpointSlices

```Bash
kubectl get endpointslices -n NAMESPACE
kubectl get endpointslices -l kubernetes.io/service-name=MY_SERVICE
```

## EndpointSlice vs Endpoints

| Feature | Endpoints | EndpointSlice |
|---|---|---|
| Max endpoints per object | Unlimited (causes performance issues) | 100 (default) |
| Update on pod change | Full object rewritten | Only affected slice updated |
| Scalability | Poor at >1000 pods | Designed for large scale |
| Default since | All versions | Kubernetes 1.21 (GA) |

## How kube-proxy Uses EndpointSlices

kube-proxy watches `EndpointSlice` objects to program iptables/IPVS rules. When a pod becomes unready (readiness probe fails), the `ready: false` condition is set on the endpoint entry and kube-proxy removes the rule.

## Commands

