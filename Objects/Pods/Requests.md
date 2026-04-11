**Resource Requests** are the minimum amount of CPU and memory that Kubernetes guarantees for a container

* Used by the **[[kube-scheduler]]** to find a node with sufficient allocatable resources to place the pod
* The scheduler only considers the node's **allocatable** resources minus the sum of all existing pod requests
* A container will always receive at least its requested amount as it may burst above it up to its limit

## Defining Requests

```YAML
spec:
  containers:
    - name: my-app
      image: my-app:v1
      resources:
        requests:
          cpu: "250m"        # 250 millicores = 0.25 CPU cores
          memory: "128Mi"    # 128 mebibytes
        limits:
          cpu: "500m"
          memory: "256Mi"
```

## CPU Units

| Value | Meaning |
|---|---|
| `1` | 1 full CPU core (or hyperthread) |
| `500m` | 500 millicores = 0.5 cores |
| `100m` | 100 millicores = 0.1 cores |

CPU is a **compressible** resource where exceeding a CPU request does not cause OOM; it causes throttling.

## Memory Units

| Value | Meaning |
|---|---|
| `128Mi` | 128 mebibytes (binary) |
| `256M` | 256 megabytes (decimal, slightly less than 256Mi) |
| `1Gi` | 1 gibibyte |

Memory is an **incompressible** resource where exceeding memory limits results in OOM kill.

## Effect of Missing Requests

If a container has no requests defined, it is classified as `BestEffort` QoS

* The scheduler treats it as requesting 0 CPU and 0 memory as it can be placed on any node
* It is the first to be evicted under node memory pressure
* It receives no CPU guarantee while it competes with all other processes

## Viewing Node Allocatable vs Requested

```Bash
kubectl describe node NODE_NAME | grep -A5 "Allocated resources"
kubectl top nodes
```

## Resource Requests vs Limits

Resource **requests** define the guaranteed minimum; resource **limits** define the enforced ceiling

```YAML
resources:
  requests:
    cpu: "250m"      # Scheduler places pod on a node with ≥250m available
    memory: "128Mi"  # Scheduler places pod on a node with ≥128Mi available
  limits:
    cpu: "500m"      # Container CPU is throttled if it exceeds this
    memory: "256Mi"  # Container is OOMKilled if it exceeds this
```

## CPU Units

| Value | Meaning |
|---|---|
| `1` | 1 full CPU core |
| `500m` | 500 millicores = 0.5 cores |
| `100m` | 100 millicores = 0.1 cores |

CPU is **compressible** — exceeding requests causes throttling, not termination.

## Memory Units

| Value | Meaning |
|---|---|
