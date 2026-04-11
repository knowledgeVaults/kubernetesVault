
```Bash
Events: Type Reason Age From Message ---- ------ ---- ---- ------- Warning Unhealthy 2m58s (x101923 over 10d) kubelet (combined from similar events): Readiness probe failed: 2026-04-05 01:30:18.136 [INFO][3393188] confd/health.go 180: Number of node(s) with BGP peering established = 0 calico/node is not ready: BIRD is not ready: BGP not established with IP_REDACTED
```

Why this happens:
* No routes between nodes
* No [[Pod Networking]]
* No access to [[CoreDNS]]
* No internet

## BGP Peering Failure Diagnosis

The error `BGP not established` means Calico nodes cannot form BGP sessions, breaking pod-to-pod routing across nodes.

```Bash
# 1. Check calico-node pod status
kubectl get pods -n kube-system -l k8s-app=calico-node
kubectl logs -n kube-system POD_NAME -c calico-node | tail -50

# 2. Check BGP peer status with calicoctl
calicoctl node status

# 3. Check network connectivity between nodes
# BGP uses TCP port 179 — ensure it is open
nc -zv NODE_IP 179

# 4. Check Calico configuration
kubectl get felixconfiguration default -o yaml
kubectl get bgpconfiguration default -o yaml
```

## Common Calico Issues

| Issue | Cause | Fix |
|---|---|---|
| BGP not established | Port 179 blocked | Open TCP 179 between nodes |
| BGP not established | AS number mismatch | Verify `BGPConfiguration.spec.asNumber` matches |
| Pods can't reach internet | IPMASQ not configured | Check `natOutgoing: true` in IPPool |
| [[Network Policies]] not enforced | Wrong CNI or Calico not healthy | Check calico-node pods are all `Running` |

```Bash
kubectl get ippool -o yaml     # Verify CIDR and natOutgoing settings
```

## Network Policy Debugging

```Bash
# Use Calico's policy viewer
calicoctl get networkpolicy --all-namespaces

# Check which policies apply to a pod
calicoctl get policy -n NAMESPACE
kubectl exec POD_NAME -- curl TARGET_IP:PORT    # Test traffic directly
```

## Checking calico-node Readiness

```Bash
kubectl get pods -n kube-system -l k8s-app=calico-node -o wide
# All pods should be Running and Ready (1/1 or 2/2)
```

A `0/1` Ready count means the BIRD BGP daemon or Felix is unhealthy. Check logs for the specific error.
