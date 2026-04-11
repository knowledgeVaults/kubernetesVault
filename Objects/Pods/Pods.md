A **Pod** is the smallest deployable unit in Kubernetes consisting of a group of one or more containers that share a [[Network Namespaces]], storage volumes, and lifecycle

* Every container in a Pod shares the same IP address and port space
* All containers in a Pod are co-scheduled on the same node and start/stop together
* Pods are ephemeral because they are not self-healing; use Deployments or ReplicaSets for resilience

## Pod Manifest

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: default
  labels:
    app: my-app
spec:
  containers:
    - name: app
      image: my-app:v1
      ports:
        - containerPort: 8080
      resources:
        requests:
          cpu: "100m"
          memory: "128Mi"
        limits:
          cpu: "500m"
          memory: "256Mi"
      env:
        - name: ENV_VAR
          value: "value"
        - name: SECRET_KEY
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: key
      envFrom:
        - configMapRef:
            name: my-configmap
  restartPolicy: Always     # Always | OnFailure | Never
```

## Immutable Fields

Most `spec` fields cannot be changed on a running Pod; exceptions are:
* `spec.containers[*].image`
* `spec.initContainers[*].image`
* `spec.activeDeadlineSeconds`
* `spec.tolerations`

## Pod Commands

```Bash
kubectl run my-pod --image=nginx -n NAMESPACE
kubectl run my-pod --image=nginx --dry-run=client -o yaml > pod.yaml
kubectl get pods [-o wide] [-n NAMESPACE]
kubectl describe pod MY_POD
kubectl delete pod MY_POD [--grace-period=0 --force]
kubectl exec -it MY_POD -- /bin/sh
kubectl logs MY_POD [-c CONTAINER] [-f] [--previous]
kubectl top pod MY_POD --containers
```

## Pod Lifecycle

```
Pending → ContainerCreating → Running → Succeeded / Failed
```

The [[kubelet]] reconciles the pod's desired state continuously by restarting containers that crash based on `restartPolicy`.

## Probe Types Summary

| Probe | Purpose | Failure Action |
|---|---|---|
| `livenessProbe` | Container is alive | Restart the container |
| `readinessProbe` | Container is ready for traffic | Remove from Service endpoints |
| `startupProbe` | Container has finished starting | Restart if startup takes too long |

## Environment Variables

```YAML
env:
  - name: MY_VAR          # Static value
    value: "hello"
  - name: POD_NAME        # From pod metadata (Downward API)
    valueFrom:
      fieldRef:
        fieldPath: metadata.name
