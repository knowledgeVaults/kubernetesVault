

## Internet Connectivity

tmp
```Bash
ping 8.8.8.8
```

tmp
```Bash
curl https://google.com
```

tmp
```Bash
nslookup google.com

# No work
#nslookup google.com ;; connection timed out; no servers could be reached
```

## Diagnosing Pod Network Issues

```Bash
# 1. Check pod IP assignment
kubectl get pod POD_NAME -o wide    # POD_IP column

# 2. Test pod-to-pod connectivity
kubectl exec POD_A -- ping -c3 POD_B_IP
kubectl exec POD_A -- curl http://POD_B_IP:PORT

# 3. Test service DNS resolution
kubectl exec POD_A -- nslookup my-service.default.svc.cluster.local
kubectl exec POD_A -- curl http://my-service.default.svc.cluster.local

# 4. Test external connectivity
kubectl exec POD_A -- ping 8.8.8.8
kubectl exec POD_A -- curl https://google.com
kubectl exec POD_A -- nslookup google.com
```

## Common Causes

| Symptom | Likely Cause |
|---|---|
| Pod has no IP | CNI plugin not installed or misconfigured |
| Pod-to-pod fails | Missing CNI routes or firewall rules between nodes |
| Service DNS fails | [[CoreDNS]] pods not running; check `kube-system` namespace |
| External DNS fails | Node DNS misconfigured; check `/etc/resolv.conf` on pod |
| [[Network Policies]] blocking | Add test pod without policies; narrow down culprit policy |

## Using a Debug Sidecar

```Bash
# Temporary debug pod in the same namespace
kubectl run net-debug --image=nicolaka/netshoot --rm -it --restart=Never \
  -n TARGET_NAMESPACE -- bash
```

`netshoot` includes `curl`, `nslookup`, `ping`, `tcpdump`, `iptables`, and other network tools.

## iptables Inspection

```Bash
# View all nat rules for a service
iptables -t nat -L -n --line-numbers | grep -A5 SERVICE_IP
```
This shows whether [[kube-proxy]] has programmed rules for the service. Empty output means rules are missing.

## Checking NetworkPolicy Impact

```Bash
# List all NetworkPolicies in the namespace
kubectl get networkpolicies -n NAMESPACE
kubectl describe networkpolicy POLICY_NAME

# Test: temporarily delete a NetworkPolicy to confirm it's the blocker
kubectl delete networkpolicy POLICY_NAME    # Revert immediately after testing
```
