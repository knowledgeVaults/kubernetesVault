
**Availability** ensures that a applications running in a cluster stay accessible and running even in nodes or components fail by distributing and running self-healing workloads

| **Feature**            | **VPA**                                               | **HPA**                                                   |
| ---------------------- | ----------------------------------------------------- | --------------------------------------------------------- |
| Scaling Method         | Increases CPU and memory of existing Pods             | Adds/Removes Pods based on load                           |
| Pod Behavior           | Restarts Pods to apply the new resource values        | Keeps existing Pods running                               |
| Traffic Spike Handling | Scaling requires the Pods to be restarted             | Instantly adds more Pods                                  |
| Cost Optimization      | Prevents over-provisioning of CPU and memory          | Avoids unnecessary idle Pods                              |
| Use Cases              | *Stateful workloads*, *CPU/memory-heavy apps*, *etc.* | *Web apps*, *microservices*, *stateless services*, *etc.* |

## Pod Replicas

Always run at least **2 replicas** for any production workload because single-replica Deployments have no fault tolerance

```YAML
spec:
  replicas: 3    # Minimum 2 for HA; 3 for rolling-update without downtime
```

## PodDisruptionBudgets

Define a PDB for every critical workload to protect against maintenance-related outages

```YAML
spec:
  minAvailable: 2    # At least 2 pods must remain during voluntary disruptions
```

## Topology Spread

Spread replicas across nodes and availability zones to prevent correlated failures

```YAML
spec:
  topologySpreadConstraints:
    - maxSkew: 1
      topologyKey: topology.kubernetes.io/zone
      whenUnsatisfiable: DoNotSchedule
      labelSelector:
        matchLabels:
          app: my-app
```

## Readiness Probes

Define readiness probes on all containers because pods that are not ready are automatically removed from Service endpoints

```YAML
readinessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
  failureThreshold: 3
```

## Resource Requests and Limits

Set appropriate requests and limits to prevent resource starvation and ensure QoS `Guaranteed` or `Burstable`

## [[Horizontal Pod Autoscaler]]

Enable HPA to handle variable load automatically, scaling replicas based on CPU, memory, or custom metrics.
