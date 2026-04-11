
Check the events of a Pod
```Bash
kubectl describe pod POD_NAME -n NAMESPACE
```

Check the logs of a Pod
```Bash
# Check the logs of a Pod
kubectl logs POD_NAME -n NAMESPACE 

# Check the logs of the previous Pod
kubectl logs POD_NAME -n NAMESPACE --previous
```

## Systematic Diagnosis

```Bash
# 1. Pod phase and conditions
kubectl get pod POD_NAME -n NAMESPACE
kubectl describe pod POD_NAME -n NAMESPACE    # Events section is key

# 2. Container logs
kubectl logs POD_NAME -n NAMESPACE
kubectl logs POD_NAME -n NAMESPACE --previous  # Logs from last crash

# 3. Events
kubectl get events -n NAMESPACE --sort-by='.lastTimestamp'
kubectl get events --field-selector involvedObject.name=POD_NAME

# 4. Exec into the container
kubectl exec -it POD_NAME -n NAMESPACE -- sh
```

## Common Pod Failure Reasons

| Reason | Cause | Fix |
|---|---|---|
| `CrashLoopBackOff` | App crashes on startup | Check `--previous` logs; fix app config |
| `OOMKilled` | Memory limit exceeded | Increase `resources.limits.memory` |
| `ImagePullBackOff` | Bad tag or missing credentials | Check image name; add `imagePullSecrets` |
| `Pending: Insufficient cpu` | No node has enough CPU | Scale cluster or lower resource requests |
| `Pending: Unschedulable` | Taints, affinity, or resource mismatch | Check node taints and pod tolerations |

## Checking Resource Limits

```Bash
kubectl describe pod POD_NAME | grep -A5 Limits
kubectl top pod POD_NAME --containers    # Compare actual vs limits
```

If actual usage is near the memory limit, the container will be OOMKilled. If CPU usage is near the limit, the container will be throttled but not killed.

## Service Account Permissions

If the app crashes with `403 Forbidden` calling the Kubernetes API:
```Bash
kubectl auth can-i list pods --as=system:serviceaccount:NAMESPACE:SA_NAME
# Create a Role and RoleBinding granting the required permissions
```

## Config and [[Secrets]] Issues

```Bash
# Verify env vars injected into pod
kubectl exec POD_NAME -- env | grep MY_VAR

# Verify mounted volume contents
kubectl exec POD_NAME -- ls /etc/config
kubectl exec POD_NAME -- cat /etc/config/app.yaml
```
