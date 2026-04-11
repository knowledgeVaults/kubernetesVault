
Check node status
```Bash
kubectl get nodes
```

tmp
```Bash
kubectl describe node NODE_NAME
```

tmp
```Bash
top
```

tmp
```Bash
df -h
```

tmp
```Bash
sudo journalctl -u SERVICE_NAME
```

Check the [[kubelet]] certificates
```Bash
openssl x509 -in /var/lib/kubelet/WORKER_NODE.crt -text
```

## Systematic Diagnosis

```Bash
# 1. Node status
kubectl get node NODE_NAME
kubectl describe node NODE_NAME | grep -A10 Conditions

# 2. Resource pressure
kubectl describe node NODE_NAME | grep -E "MemoryPressure|DiskPressure"
# On the node directly:
free -h       # Memory
df -h         # Disk
top           # CPU/process load

# 3. kubelet status
systemctl status kubelet
journalctl -u kubelet -n 100

# 4. kubelet certificate validity
openssl x509 -in /var/lib/kubelet/pki/kubelet-client-current.pem -noout -dates
```

## Common Worker Node Issues

| Symptom          | Cause                       | Fix                                             |
| ---------------- | --------------------------- | ----------------------------------------------- |
| `NotReady`       | kubelet not running         | `systemctl restart kubelet`                     |
| `NotReady`       | Certificate expired         | Renew with `kubeadm certs renew kubelet-client` |
| `MemoryPressure` | Too many pods / memory leak | Evict pods, increase node memory, reduce limits |
| `DiskPressure`   | Image or log accumulation   | `crictl rmi --prune`, clean up log files        |
| Pods evicted     | Node OOM                    | Review pod memory limits; add more nodes        |

## [[containerd]] / CRI Issues

```Bash
# Check container runtime status
systemctl status containerd
journalctl -u containerd -n 50

# Verify kubelet can reach the CRI socket
crictl info
crictl ps
```

## Node Pressure Conditions

```Bash
kubectl describe node NODE_NAME | grep -A3 "Conditions:"
```

`MemoryPressure=True` or `DiskPressure=True` triggers eviction of pods. Address the underlying cause before uncordoning.

## Rejoining a Node After Repair

```Bash
# After fixing the issue, restart kubelet and verify
systemctl start kubelet
kubectl get node NODE_NAME    # Should show Ready within ~60 seconds
kubectl uncordon NODE_NAME    # Re-enable scheduling if previously cordoned
```

## Viewing Node Events

```Bash
kubectl get events --field-selector involvedObject.kind=Node,involvedObject.name=NODE_NAME
```

Events show kubelet restarts, [[Taints]] additions, and condition changes with timestamps.
