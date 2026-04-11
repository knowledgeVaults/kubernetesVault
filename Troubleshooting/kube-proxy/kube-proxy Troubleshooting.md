
Check that [[kube-proxy]] is running
```Bash
kubectl get pods -n kube-system | grep kube-proxy
```

Check kube-proxy logs
```Bash
kubectl logs kube-proxy -n kube-system
```

Check if the config map is correctly defined and if the config file for running the kube-proxy binary is correct

Check if kube-proxy is running inside the container

## Systematic Diagnosis

```Bash
# 1. Verify kube-proxy is running
kubectl get pods -n kube-system -l k8s-app=kube-proxy
kubectl logs -n kube-system -l k8s-app=kube-proxy

# 2. Check the kube-proxy ConfigMap
kubectl get configmap kube-proxy -n kube-system -o yaml
# Verify: mode field, clusterCIDR, and kubeconfig path

# 3. Check iptables rules for a Service
SERVICE_IP=$(kubectl get svc SERVICE_NAME -o jsonpath='{.spec.clusterIP}')
iptables -t nat -L -n | grep $SERVICE_IP

# 4. Verify kube-proxy is running inside its container
kubectl exec -n kube-system KUBE_PROXY_POD -- ps aux | grep kube-proxy
```

## Common Issues

| Symptom | Cause | Fix |
|---|---|---|
| Service [[ClusterIP]] unreachable | kube-proxy not running | Restart [[Daemon Sets]] pod |
| Stale iptables rules | kube-proxy crashed; rules not updated | Restart kube-proxy |
| Wrong mode in config | [[Config Maps]] has wrong `mode` value | Edit ConfigMap and restart pods |
| [[NodePort]] not accessible | Firewall blocks the nodePort range | Open 30000-32767 on node firewalls |

## IPVS Mode Inspection

```Bash
# If using IPVS mode
ipvsadm -Ln                          # List all virtual servers
ipvsadm -Ln | grep SERVICE_IP        # Find specific service rules
```

IPVS rules are much simpler to read than iptables chains for large clusters.

## Verifying ConfigMap

```Bash
kubectl get configmap kube-proxy -n kube-system -o yaml | grep -A5 "config.conf"
```

Key fields to verify: `mode` (iptables/ipvs), `clusterCIDR`, and `iptables.masqueradeAll`.

## Restarting kube-proxy

```Bash
kubectl rollout restart daemonset kube-proxy -n kube-system
kubectl rollout status daemonset kube-proxy -n kube-system
```

A restart clears stale iptables/IPVS rules and rebuilds them from the current Service/[[Endpoint Slices]] state.
