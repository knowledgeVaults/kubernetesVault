The **Cluster Autoscaler (CA)** automatically adjusts the number of nodes in a cluster based on pending pod scheduling failures and node utilization

* Scales **out** (adds nodes) when pods cannot be scheduled due to insufficient resources
* Scales **in** (removes nodes) when nodes are consistently underutilized and their pods can be safely rescheduled elsewhere
* Cloud-provider-specific: integrates with AWS Auto Scaling Groups, GCP Managed Instance Groups, Azure VMSS, and others
* Runs as a `[[Deployments]]` typically in the `kube-system` namespace

## Scale-Out Trigger

The CA monitors the [[kube-apiserver]] for pods in `Pending` state due to resource constraints

* When a pod cannot be scheduled (`Insufficient cpu`, `Insufficient memory`), CA evaluates which node group could accommodate it
* CA simulates scheduling on a hypothetical new node of each node group type
* If a node group can accommodate the pending pod, CA requests a scale-out from the cloud provider
* New node must become `Ready` within a configurable timeout (default 15 minutes) or CA marks the scale attempt as failed

## Scale-In Trigger

CA checks for underutilized nodes periodically (default every 10 seconds)

* A node is considered underutilized if its total requested resources fall below a threshold (default 50% for both CPU and memory)
* CA verifies that all pods on the node can be rescheduled elsewhere before initiating removal
* Pods that prevent scale-in: pods with `[[PodDisruptionBudgets]]` violations, pods with local storage, pods not managed by a controller, and system-critical pods

## Key Configuration Flags

| Flag | Default | Description |
|---|---|---|
| `--scale-down-delay-after-add` | 10m | Wait after scale-out before checking scale-in |
| `--scale-down-unneeded-time` | 10m | Node must be unneeded for this duration |
| `--max-node-provision-time` | 15m | Max time to wait for a new node to become Ready |
| `--skip-nodes-with-local-storage` | true | Prevents eviction of pods using local storage |

## Annotation: Preventing Scale-In

```YAML
metadata:
  annotations:
    cluster-autoscaler.kubernetes.io/safe-to-evict: "false"
```
