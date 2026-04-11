The [[kube-controller-manager]] is a single binary that bundles all of the core Kubernetes controllers into one process

* Watches the [[kube-apiserver]] for changes to cluster resources via the **[[List+Watch]]** mechanism
* Each embedded controller runs its own reconciliation loop to drive actual state toward desired state
* Communicates only with the kube-apiserver — never directly with [[etcd]] or other components

## Controller Types

There are many built-in controllers, each watching a specific resource kind

| Controller                      | Watches          | Reconciles                                       |
| ------------------------------- | ---------------- | ------------------------------------------------ |
| [[Deployments]] Controller      | `Deployment`     | Creates/scales [[ReplicaSets]] objects           |
| ReplicaSet Controller           | `ReplicaSet`     | Creates/deletes `Pod` objects                    |
| Node Controller                 | `Node`           | Applies taints on unhealthy nodes; evicts pods   |
| Job Controller                  | `Job`            | Creates Pods for batch tasks; retries on failure |
| [[Service Accounts]] Controller | `Namespace`      | Creates `default` ServiceAccount per namespace   |
| Namespace Controller            | `Namespace`      | Cleans up resources in terminating namespaces    |
| Endpoints Controller            | `Service`, `Pod` | Populates `Endpoints` objects                    |

## Static Pod Manifest

In [[kubeadm]] clusters, runs as a Static Pod

```Bash
cat /etc/kubernetes/manifests/kube-controller-manager.yaml
```

## Key Flags

| Flag | Description |
|---|---|
| `--leader-elect` | Enable HA [[Architecture/Leader Election]] (default true) |
| `--node-monitor-grace-period` | Time before marking node as unhealthy (default 40s) |
| `--pod-eviction-timeout` | Time before evicting pods from unknown nodes (default 5m) |
| `--controllers` | Comma-separated list of controllers to enable (`*` = all) |

## Health and Logs

```Bash
kubectl get pod -n kube-system -l component=kube-controller-manager
kubectl logs -n kube-system kube-controller-manager-NODENAME
kubectl get events --all-namespaces | grep -i controller
```

## Leader Election for HA

In multi-master setups, only one kube-controller-manager instance is active at a time

```Bash
kubectl get lease kube-controller-manager -n kube-system -o yaml
# holderIdentity shows the active leader
```
