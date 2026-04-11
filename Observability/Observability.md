
**Observability** in Kubernetes encompasses the tools and practices for understanding the internal state of cluster components and workloads through **metrics**, **logs**, and **traces**

## The Three Pillars

### Metrics

Quantitative measurements over time (*resource usage*, *error rates*, *latency*, *etc.*)

* **[[Metrics Server]]**: in-cluster scraper for CPU/memory; powers `kubectl top` and HPA/VPA
* **Prometheus**: full-featured time-series database; scrapes metrics from pods, nodes, and Kubernetes APIs
* **Grafana**: visualization layer for Prometheus data

### Logs

Textual records of events from containers and system components

* **[[Fluent Bit]]** / **Fluentd**: log shippers running as DaemonSets; forward logs to [[Elasticsearch]], Loki, or cloud services
* **Elasticsearch + Kibana**: log storage and search (EFK stack)
* **Loki**: lightweight log aggregation designed for use with Grafana

### Traces

Distributed tracing captures request flows across microservices

* **OpenTelemetry (OTel)**: vendor-neutral SDK and collector for trace/metric/log instrumentation
* **Jaeger** / **Tempo**: tracing backends that store and query spans

## Key Observability Components in Kubernetes

| Component | Category | Purpose |
|---|---|---|
| Metrics Server | Metrics | In-memory CPU/memory for HPA, kubectl top |
| Prometheus | Metrics | Persistent time-series metrics collection |
| Grafana | Metrics | Dashboards and alerting |
| Fluent Bit | Logs | Lightweight node-level log shipper |
| Elasticsearch | Logs | Log storage and full-text search |
| [[cAdvisor]] | Metrics | Container-level resource stats (in [[kubelet]]) |
| Custom Metrics Adapter | Metrics | Exposes external metrics to HPA |

## Accessing Metrics

```Bash
kubectl top pods [-n NAMESPACE] [--containers]
kubectl top nodes
```

## Health Probes

```YAML
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 10
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
startupProbe:
  httpGet:
    path: /healthz
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```
