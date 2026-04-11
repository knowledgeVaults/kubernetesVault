
A **Custom Metrics Adapter** extends the Kubernetes metrics API to expose application-level or external metrics that the **Horizontal Pod Autoscaler (HPA)** can use for scaling decisions beyond the built-in CPU and memory metrics

* Implements the `custom.metrics.k8s.io` or `external.metrics.k8s.io` API extension
* The HPA queries these APIs to get current metric values and compares them against targets
* Common adapters: Prometheus Adapter, KEDA, Datadog Cluster Agent, Stackdriver Adapter

## Why Custom Metrics

The default [[Metrics Server]] only provides CPU and memory. Many real-world scaling triggers require application-level signals:

* HTTP requests per second
* Message queue depth
* Active WebSocket connections
* Cache hit ratio
* Database connection pool utilisation

## Prometheus Adapter

The most common adapter that translates Prometheus queries into Kubernetes custom metrics API responses

### Installation

```Bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus-adapter prometheus-community/prometheus-adapter \
  --namespace monitoring \
  --set prometheus.url=http://prometheus-operated.monitoring.svc
```

### Configuration

```YAML
rules:
  - seriesQuery: 'http_requests_total{namespace!="",pod!=""}'
    resources:
      overrides:
        namespace: {resource: "namespace"}
        pod: {resource: "pod"}
    name:
      matches: "^(.*)_total$"
      as: "${1}_per_second"
    metricsQuery: 'rate(<<.Series>>{<<.LabelMatchers>>}[2m])'
```

## HPA Using a Custom Metric

```YAML
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
  metrics:
    - type: Pods
      pods:
        metric:
          name: http_requests_per_second
        target:
          type: AverageValue
          averageValue: "500"
```

## Verifying the Custom Metrics API

```Bash
kubectl get --raw /apis/custom.metrics.k8s.io/v1beta1
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1/namespaces/default/pods/*/http_requests_per_second"
```
