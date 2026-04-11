
Some `kubectl` Commands for deleting Kubernetes resources.

## Delete by Name

```Bash
# Delete a single resource
kubectl delete pod POD_NAME [-n NAMESPACE]
kubectl delete service SERVICE_NAME
kubectl delete deployment DEPLOYMENT_NAME
kubectl delete node NODE_NAME
```

## Delete by File

```Bash
# Delete resources defined in a manifest
kubectl delete -f FILENAME.yaml

# Delete all resources in a directory
kubectl delete -f ./manifests/
```

## Delete All Resources of a Type

```Bash
# Delete all pods in a namespace
kubectl delete pods --all -n NAMESPACE

# Delete all resources of all types in a namespace
kubectl delete all --all -n NAMESPACE
```

## Delete with Label Selector

```Bash
kubectl delete pods -l app=my-app -n NAMESPACE
kubectl delete all -l env=staging
```

## Force Delete (Stuck Pods)

```Bash
# Force delete without waiting for graceful termination
kubectl delete pod POD_NAME --grace-period=0 --force -n NAMESPACE
```

## Namespace Deletion

```Bash
kubectl delete namespace NAMESPACE
# All resources in the namespace are garbage collected automatically
```

## Replace (Delete + Recreate)

```Bash
kubectl replace --force -f FILENAME.yaml
# Equivalent to: kubectl delete -f FILENAME.yaml && kubectl apply -f FILENAME.yaml
```

## Proxy (for direct API access)

```Bash
kubectl proxy
# Then: curl http://localhost:8001/api/v1/namespaces/default/pods/POD_NAME
```

## Drain a Node Before Maintenance

```Bash
kubectl cordon NODE_NAME        # Mark unschedulable
kubectl drain NODE_NAME --ignore-daemonsets --delete-emptydir-data
kubectl uncordon NODE_NAME      # Return to service after maintenance
```

## Wait for Deletion

```Bash
# Wait until the pod no longer exists
kubectl delete pod POD_NAME --wait=true

# Wait with timeout
kubectl wait --for=delete pod/POD_NAME --timeout=60s
```

## Finalizer Removal (Stuck Resources)

If a resource is stuck in `Terminating` state due to a finalizer:
```Bash
kubectl patch RESOURCE NAME -p '{"metadata":{"finalizers":[]}}' --type=merge
```
