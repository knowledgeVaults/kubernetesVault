
* A cluster add-on that runs inside your cluster
* An in-memory solution so it doesn't store the metrics on the disk so you can't see historical performance data

## Installing [[Metrics Server]]

```Bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# In environments with self-signed certs (e.g. minikube):
kubectl patch deployment metrics-server -n kube-system \
  --type=json -p='[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}]'
```

## What Metrics Server Provides

Metrics Server exposes the `metrics.k8s.io/v1beta1` API

```Bash
kubectl top nodes
kubectl top pods -n NAMESPACE
kubectl top pods --containers    # Per-container breakdown
```

## Data Flow

```
kubelet (cAdvisor) → Metrics Server → metrics.k8s.io API
                                           │
                          ┌────────────────┼────────────────┐
                          ▼                ▼                ▼
                    kubectl top       HPA controller   VPA Recommender
```

## Limitations

* In-memory only since it no historical data retention
* Aggregates metrics at 15-second intervals
* Only CPU and memory (No disk, network, or custom metrics)
* For historical data or custom metrics, deploy Prometheus

## Verifying Installation

```Bash
kubectl get deployment metrics-server -n kube-system
kubectl get apiservice v1beta1.metrics.k8s.io
# Should show: AVAILABLE=True
```


## Troubleshooting
```Bash
# If kubectl top returns error: metrics not available
kubectl logs -n kube-system deploy/metrics-server
# Common issues: TLS errors → add --kubelet-insecure-tls flag
# Missing readiness probe → metrics-server pod itself not ready
```

## Resource Usage

Metrics Server itself uses minimal resources with typically 10–40m CPU and 30–60Mi memory per cluster. Scale the replica count or increase memory limits for large clusters with hundreds of nodes.

## Checking API Availability

```Bash
kubectl get --raw /apis/metrics.k8s.io/v1beta1/nodes
kubectl get --raw /apis/metrics.k8s.io/v1beta1/namespaces/default/pods
```

If these return 404 or errors, Metrics Server is not running or its APIService is not registered.
