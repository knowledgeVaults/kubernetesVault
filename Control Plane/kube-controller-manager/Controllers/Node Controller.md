The **Node Controller** monitors the health of cluster nodes and reconciles node state with the actual infrastructure

* Watches `Node` objects and their heartbeat signals from kubelets
* Applies taints to nodes that lose connectivity and evicts pods from nodes declared `Unknown`
* Works in coordination with the [[cloud-controller-manager]] in cloud deployments

## Node Registration

When a new [[kubelet]] starts, it registers itself by creating or updating a `Node` object in the [[kube-apiserver]]

* The Node Controller assigns the initial CIDR range to the new node (if CIDR auto-assignment is enabled)
* It also monitors that the node's capacity and allocatable resources are correctly reported

## Heartbeat Mechanism

Kubelets send heartbeats to maintain their node's health status

* **Node Lease**: a `Lease` object in the `kube-node-lease` namespace, updated every `nodeStatusUpdateFrequency` (default 10s)
* **Node Status**: the `.status.conditions` on the Node object, updated less frequently

The Node Controller checks the Lease age to determine if a node is still alive.

## Node Condition: NotReady → Unknown

If a node's Lease is not renewed within `node-monitor-grace-period` (default 40s):

1. The Node Controller sets the node condition to `Unknown`
2. After `pod-eviction-timeout` (default 5 minutes), pods on the unknown node are marked for eviction
3. New pods are not scheduled to the node while it is `NotReady` or `Unknown`

## Taints Applied by the Node Controller

| [[Taints]] | Condition |
|---|---|
| `node.kubernetes.io/not-ready` | Node condition is `NotReady` |
| `node.kubernetes.io/unreachable` | Node condition is `Unknown` |
| `node.kubernetes.io/memory-pressure` | Node reports `MemoryPressure` |
| `node.kubernetes.io/disk-pressure` | Node reports `DiskPressure` |
| `node.kubernetes.io/pid-pressure` | Node reports `PIDPressure` |

These taints cause pods without matching tolerations to be evicted.

## Rate Limiting Eviction

The Node Controller rate-limits pod evictions during large-scale failures

* `--node-eviction-rate` (default 0.1): pods evicted per second per zone when a zone is healthy
* `--secondary-node-eviction-rate` (default 0.01): reduced rate when many nodes in a zone fail simultaneously
* If more than 55% of nodes in a zone become `NotReady`, eviction stops to prevent cascading failures
