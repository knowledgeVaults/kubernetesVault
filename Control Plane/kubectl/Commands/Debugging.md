
Some `kubectl` Commands for diagnosing and troubleshooting cluster resources.

## Generating Manifests Without Creating

```Bash
# Generate a Pod manifest without applying it
kubectl run POD_NAME --image=IMAGE --dry-run=client -o yaml

# Generate a Deployment manifest
kubectl create deployment NAME --image=IMAGE --dry-run=client -o yaml > deploy.yaml
```

## Exec into a Container

```Bash
# Open an interactive shell
kubectl exec -it POD_NAME -- /bin/sh
kubectl exec -it POD_NAME -c CONTAINER_NAME -- /bin/bash

# Run a one-off command
kubectl exec POD_NAME -- cat /etc/resolv.conf
kubectl exec POD_NAME -- env
```

## Port Forwarding

```Bash
# Forward a local port to a pod port
kubectl port-forward pod/POD_NAME 8080:80

# Forward to a service
kubectl port-forward service/SERVICE_NAME 8080:80
```

## Run a Debug Pod

```Bash
# Temporary debug pod that auto-deletes on exit
kubectl run debug --image=busybox --rm -it --restart=Never -- sh

# Debug with network tools
kubectl run netdebug --image=nicolaka/netshoot --rm -it --restart=Never -- bash
```

## Resource Usage

```Bash
kubectl top pod [POD_NAME] [-n NAMESPACE]
kubectl top node
```

## Events

```Bash
kubectl get events -n NAMESPACE --sort-by='.lastTimestamp'
kubectl get events --field-selector involvedObject.name=POD_NAME
kubectl get events --field-selector reason=FailedScheduling
```

## Proxy to [[kube-apiserver]]

```Bash
kubectl proxy &
curl http://localhost:8001/api/v1/namespaces/default/pods
```

## Copy Files

```Bash
kubectl cp POD_NAME:/path/to/file ./local-file
kubectl cp ./local-file POD_NAME:/path/to/file
```

## Verbose Output

```Bash
# Level 6: shows HTTP requests
kubectl get pods --v=6

# Level 9: shows full HTTP request and response bodies
kubectl get pods --v=9
```

Verbose levels are useful for diagnosing API connectivity issues or understanding how kubectl builds its requests.

## [[Admission Controllers]] Inspection

```Bash
# View enabled admission plugins
kube-apiserver -h | grep enable-admission-plugins

# In kubeadm
kubectl exec kube-apiserver-NODE -n kube-system -- \
  kube-apiserver -h | grep enable-admission-plugins
```
