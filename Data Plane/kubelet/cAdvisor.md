**[[cAdvisor]] (Container Advisor)** is an open-source resource usage and performance analysis tool built directly into the [[kubelet]]

* Automatically discovers all containers running on a node and collects CPU, memory, filesystem, and network usage metrics
* Exposes collected metrics via the kubelet's built-in HTTP API (`/metrics/cadvisor`) in Prometheus format
* Metrics are consumed by the **[[Metrics Server]]** (for HPA/VPA) and directly by Prometheus for monitoring

## Metrics Exposed

cAdvisor collects resource usage at both the node and container level

| Metric | Description |
|---|---|
| `container_cpu_usage_seconds_total` | Cumulative CPU time consumed per container |
| `container_memory_usage_bytes` | Current memory usage including cache |
| `container_memory_working_set_bytes` | Memory actively in use (used by OOM killer threshold) |
| `container_fs_usage_bytes` | Filesystem usage per container |
| `container_network_receive_bytes_total` | Total bytes received over the network |
| `container_network_transmit_bytes_total` | Total bytes transmitted over the network |

## Accessing cAdvisor Metrics

cAdvisor metrics are served by the kubelet on port `10250`

```Bash
# From the node
curl -sk https://NODE_IP:10250/metrics/cadvisor | grep container_cpu

# Via kubectl (requires authentication)
kubectl get --raw "/api/v1/nodes/NODE_NAME/proxy/metrics/cadvisor"
```

## Integration with Prometheus

In a cluster monitored by Prometheus (e.g., using kube-prometheus-stack), cAdvisor metrics are scraped automatically via the `kubelet` ServiceMonitor

```YAML
# Prometheus job to scrape cAdvisor
- job_name: kubernetes-cadvisor
  scheme: https
  metrics_path: /metrics/cadvisor
  kubernetes_sd_configs:
    - role: node
```

## Role in the Metrics Pipeline

```
containers → cAdvisor (in kubelet) → /metrics/cadvisor endpoint
                                          │
                              Prometheus scrape → Grafana dashboards
                              Metrics Server  → HPA / VPA decisions
                              kubectl top pod/node
```
