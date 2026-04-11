
Some `kubectl` Commands for viewing, inspecting, and querying cluster resources.

## Cluster Information

```Bash
kubectl version                        # Client and server versions
kubectl cluster-info                   # API server and DNS endpoints
kubectl get nodes [-o wide]            # List all nodes with status
kubectl top nodes                      # CPU and memory usage per node (requires metrics-server)
kubectl api-resources                  # List all available resource kinds
kubectl api-versions                   # List all available API versions
kubectl explain RESOURCE               # Show field documentation
kubectl explain pod.spec.containers    # Deep field documentation
```

## Resource Listing

```Bash
kubectl get all -n NAMESPACE           # All common resources in namespace
kubectl get pods [-o wide] [-n NAMESPACE] [-A]
kubectl get deployments
kubectl get services
kubectl get replicasets
kubectl get configmaps
kubectl get secrets
kubectl get pvc                        # PersistentVolumeClaims
kubectl get pv                         # PersistentVolumes
kubectl get events -o wide [-n NAMESPACE]
kubectl get events --field-selector reason=FailedScheduling
```

## Detailed Inspection

```Bash
kubectl describe pod POD_NAME [-n NAMESPACE]
kubectl describe node NODE_NAME
kubectl describe service SERVICE_NAME
kubectl describe deployment DEPLOY_NAME
```

## Logs

```Bash
kubectl logs POD_NAME [-n NAMESPACE]
kubectl logs POD_NAME -c CONTAINER_NAME    # Multi-container pod
kubectl logs -f POD_NAME                   # Follow (tail -f)
kubectl logs --previous POD_NAME           # Logs from crashed previous container
kubectl logs -l app=my-app --all-containers # All pods matching a label
```

## Context and Config

```Bash
kubectl config view [--minify]
kubectl config get-contexts
kubectl config current-context
```

## [[JSONPath]] Queries

```Bash
kubectl get nodes -o jsonpath='{.items[*].metadata.name}'
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{" "}{.status.phase}{"\n"}{end}'
kubectl get nodes -o custom-columns=NAME:.metadata.name,VERSION:.status.nodeInfo.kubeletVersion
```
