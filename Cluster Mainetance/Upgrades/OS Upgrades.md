**OS Upgrades** on Kubernetes nodes require careful draining and cordoning to avoid workload disruption

* Kubernetes does not manage the underlying OS — administrators are responsible for patching the node OS
* The upgrade process involves safely evicting pods from the node, upgrading the OS, and then returning the node to service

## Upgrade Process

### 1. Cordon the Node

Marking the node as unschedulable prevents new pods from being assigned to it

```Bash
kubectl cordon NODE_NAME
```

* The node's `spec.unschedulable` field is set to `true`
* Existing pods continue running on the node; only new scheduling is blocked

### 2. Drain the Node

Draining evicts all non-[[Daemon Sets]] pods from the node, triggering rescheduling on other nodes

```Bash
kubectl drain NODE_NAME --ignore-daemonsets --delete-emptydir-data
```

* `--ignore-daemonsets`: DaemonSet pods cannot be evicted and are left in place
* `--delete-emptydir-data`: allows eviction of pods using `emptyDir` volumes (data will be lost)
* The drain command respects `PodDisruptionBudgets` — it will block if eviction would violate them

### 3. Perform the OS Upgrade

With the node drained, perform the OS-level maintenance

```Bash
apt upgrade -y       # Debian/Ubuntu
dnf upgrade -y       # RHEL/Fedora
reboot
```

### 4. Uncordon the Node

Once the node is back online and healthy, make it schedulable again

```Bash
kubectl uncordon NODE_NAME
```

* The scheduler will now consider the node for new pod placement
* Previously drained pods are not automatically moved back — they stay wherever they were rescheduled

## Considerations

* For worker nodes, drain one at a time to maintain workload availability
* For control plane nodes, ensure [[etcd]] quorum is maintained throughout (Never drain more than one control plane node simultaneously in a 3-node etcd cluster)

## Validating Node Health Post-Upgrade

After uncordoning, verify the node returned to a healthy state

```Bash
kubectl get node NODE_NAME
kubectl describe node NODE_NAME | grep -A5 Conditions
kubectl get pods --all-namespaces --field-selector spec.nodeName=NODE_NAME
```

Monitor the node for a few minutes before moving on to the next one to catch any post-reboot issues early.
