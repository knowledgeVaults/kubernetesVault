A **[[Daemon Sets]]** ensures that a copy of a specific Pod runs on every node (or every node matching a selector) in the cluster

* When a new node is added, the DaemonSet automatically schedules a pod on it
* When a node is removed, the DaemonSet pod is garbage collected
* Used for cluster-wide agents: log shippers, monitoring collectors, network plugins, storage drivers

## DaemonSet Manifest

```YAML
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluent-bit
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: fluent-bit
  template:
    metadata:
      labels:
        app: fluent-bit
    spec:
      tolerations:
        - key: node-role.kubernetes.io/control-plane
          operator: Exists
          effect: NoSchedule
      containers:
        - name: fluent-bit
          image: fluent/fluent-bit:latest
          volumeMounts:
            - name: varlog
              mountPath: /var/log
      volumes:
        - name: varlog
          hostPath:
            path: /var/log
```

## [[Node Selectors]] and Affinity

Limit the DaemonSet to a subset of nodes

```YAML
spec:
  template:
    spec:
      nodeSelector:
        kubernetes.io/os: linux
```

## Update Strategy

```YAML
updateStrategy:
  type: RollingUpdate          # Default: updates one pod at a time
  rollingUpdate:
    maxUnavailable: 1
  # type: OnDelete             # Manual: only updates when pod is deleted
```

## Common Use Cases

| Use Case | Example |
|---|---|
| Log aggregation | [[Fluent Bit]], Fluentd |
| Node monitoring | Prometheus node-exporter, Datadog agent |
| Network plugin | Calico, Cilium (CNI DaemonSet) |
| Storage | Ceph node daemon, Longhorn |
| Security | Falco, Sysdig |

## Commands

```Bash
kubectl get daemonset -n kube-system
kubectl describe daemonset fluent-bit -n kube-system
kubectl rollout status daemonset/fluent-bit -n kube-system
```

## DaemonSet vs [[Deployments]]

| Feature | DaemonSet | Deployment |
|---|---|---|
| Pod placement | One per node (or matching nodes) | Scheduler decides |
| Replica count | Tied to node count | Fixed `spec.replicas` |
| Use case | System agents | Application workloads |
| Node cordon | No new pods on cordoned nodes | No change |

## Scheduling on Control Plane Nodes

By default, system taints prevent DaemonSet pods from running on control plane nodes. Add a [[Tolerations]] to include them:

```YAML
tolerations:
  - key: node-role.kubernetes.io/control-plane
    operator: Exists
    effect: NoSchedule
```

