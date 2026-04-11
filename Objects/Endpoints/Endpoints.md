
**Endpoints** are API resources that store the actual network locations of Pods behind a Service

* Lists which Pods a Service should send traffic to

**Endpoints** are Kubernetes objects that store the actual IP:port pairs of Pods backing a Service

* Automatically created and managed by the Endpoints controller when a Service has a `spec.selector`
* Updated in real-time as pods become ready or unready
* [[kube-proxy]] watches Endpoints (and EndpointSlices) to program iptables/IPVS rules

## Structure

```YAML
apiVersion: v1
kind: Endpoints
metadata:
  name: my-service      # Must match the Service name
  namespace: default
subsets:
  - addresses:
      - ip: 10.1.0.5
        nodeName: worker-1
        targetRef:
          kind: Pod
          name: my-pod-abc
          namespace: default
      - ip: 10.1.0.6
    ports:
      - port: 8080
        protocol: TCP
```

## Manual Endpoints (Serviceless External Services)

Create a Service without a selector and manually manage Endpoints to route to external IPs

```YAML
# Service with no selector
apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  ports:
    - port: 5432
---
# Manual Endpoints pointing to external host
apiVersion: v1
kind: Endpoints
metadata:
  name: external-db
subsets:
  - addresses:
      - ip: 203.0.113.10
    ports:
      - port: 5432
```

## Commands

```Bash
kubectl get endpoints [-n NAMESPACE]
kubectl get ep my-service    # Short alias
kubectl describe endpoints my-service
```

## Endpoints vs EndpointSlices

EndpointSlices (GA in 1.21) replaced Endpoints for scalability as each slice holds ≤100 endpoints, avoiding the megaobject problem. kube-proxy uses EndpointSlices by default on modern clusters.


## When Endpoints Are Empty

If `kubectl get endpoints SERVICE_NAME` shows no addresses:
* No pods match the Service's `spec.selector`
* All matching pods are in `NotReady` state (readiness probe failing)
* The namespace or pod labels are mismatched

```Bash
kubectl get pods -l app=my-app -o wide    # Verify labels match selector
kubectl describe pod POD_NAME | grep "Readiness"
```
