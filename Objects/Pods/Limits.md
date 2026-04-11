
**Resource Limits** define the maximum amount of CPU and memory a container is allowed to consume

* Enforced by the Linux kernel's cgroups mechanism on the node
* Containers that exceed their **memory limit** are terminated with an `OOMKilled` (Out of Memory) error
* Containers that exceed their **CPU limit** are throttled (slowed down, not killed)

## Defining Limits

```YAML
spec:
  containers:
    - name: my-app
      image: my-app:v1
      resources:
        requests:
          cpu: "250m"
          memory: "128Mi"
        limits:
          cpu: "500m"       # Container cannot use more than 0.5 CPU cores
          memory: "256Mi"   # Container is killed if it tries to exceed 256Mi
```

## CPU Limiting (Throttling)

CPU is a compressible resource where the kernel limits how much CPU time a container gets per period

* The CFS (Completely Fair Scheduler) quota enforces the limit
* A container with `cpu: 500m` is throttled to 50% of one CPU core per 100ms CFS period
* CPU throttling causes latency spikes that set limits high enough to avoid excessive throttling

## Memory Limiting (OOM Kill)

Memory is an incompressible resource because there is no graceful degradation

* When a container tries to allocate memory beyond its limit, the OOM killer terminates it
* The container enters `OOMKilled` state; Kubernetes restarts it based on the pod's `restartPolicy`
* Symptoms: `kubectl describe pod` shows `OOMKilled`, `kubectl logs --previous` shows no graceful shutdown

## QoS Impact

| Configuration | QoS Class | Eviction Priority |
|---|---|---|
| requests == limits (all containers) | `Guaranteed` | Last to be evicted |
| requests < limits or mixed | `Burstable` | Middle priority |
| No requests or limits | `BestEffort` | First to be evicted |

## Checking Limit Enforcement

```Bash
kubectl get pod POD_NAME -o jsonpath='{.status.containerStatuses[*].lastState.terminated.reason}'
# OOMKilled → memory limit was exceeded
kubectl top pod POD_NAME --containers
```

## Detecting Throttling

CPU throttling is invisible to the container — the process doesn't crash, it just gets less CPU time. Detect it via metrics:

```Bash
# Prometheus metric
container_cpu_cfs_throttled_periods_total / container_cpu_cfs_periods_total
# If this ratio is high (>25%), the CPU limit is too low
```

Or via kubectl:
```Bash
kubectl top pod MY_POD --containers    # Check if actual CPU is near the limit
```

## Memory OOM Debugging

```Bash
