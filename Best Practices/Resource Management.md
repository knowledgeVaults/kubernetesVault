## Resource Requests and Limits

Always define CPU and memory requests and limits on every container

* **Requests** are used by the scheduler to place pods on nodes with sufficient capacity
* **Limits** enforce a ceiling; exceeding CPU limits throttles the container, exceeding memory limits triggers OOM kill
* Never set limits without requests — it leads to incorrect scheduling decisions
* A ratio of 1:2 to 1:4 (request:limit) is common for bursty workloads

```YAML
resources:
  requests:
    cpu: "250m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "256Mi"
```

## LimitRanges

Use [[Limit Ranges]] objects to enforce default resource constraints at the namespace level

* Prevents unguarded pods (no resource spec) from consuming unbounded resources
* Define per-container defaults and maximums so teams without explicit configs are still bounded

## ResourceQuotas

Apply [[Resource Quotas]] to each namespace to prevent one team from exhausting cluster-wide resources

* Limit total CPU, memory, pod count, service count, and PVC count per namespace
* Quotas enforce collective limits — the scheduler still uses per-pod requests for placement

## Quality of Service Classes

Kubernetes assigns a QoS class based on resource configuration, affecting eviction priority

| QoS Class | Condition | Eviction Priority |
|---|---|---|
| `Guaranteed` | requests == limits for all containers | Last evicted |
| `Burstable` | requests < limits or partial config | Middle |
| `BestEffort` | no requests or limits defined | First evicted |

## Vertical and Horizontal Autoscaling

Use HPA for stateless workloads and VPA for stateful or singleton services

* **HPA**: scales replica count based on CPU, memory, or custom metrics
* **VPA**: adjusts resource requests/limits automatically based on observed usage
* Do not run HPA and VPA on the same workload targeting the same resource metric — they conflict

## Monitoring Resource Usage

Use `kubectl top` and Prometheus to detect over- or under-provisioned workloads

```Bash
kubectl top pods -n NAMESPACE
kubectl top nodes
```

Regularly review VPA recommendations to right-size workloads that were initially guessed.
