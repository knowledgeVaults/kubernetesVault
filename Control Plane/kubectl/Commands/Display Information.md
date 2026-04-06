
Display client and server versions
```Bash
kubectl version
```

Summarize all resources in the current namespace
```Bash
kubectl get all
```

List cluster nodes 
```Bash
kubectl get nodes [-o wide]
```

List Pods
```Bash
kubectl get pods [-o wide] [-n NAMESPACE]
```

List deployments
```Bash
kubectl get deployments
```

List services
```Bash
kubectl get services
```


Display all the API resource types that your Kubernetes cluster supports
```Bash
kubectl api-resources
```

```Bash
kubectl explain RESOURCE
```

tmp
```Bash
kubectl get events -o wide [-n NAMESPACE]
```

tmp
```Bash
kubectl logs OBJECT_NAME -n NAMESPACE
```

tmp
```Bash
kubectl logs -f POD_NAME [CONTAINER_NAME]
```

View enabled admission controllers
```Bash
kube-apiserver -h | grep enable-admission-plugins

# If using kubeadm
kubectl exec kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep enable-admission-plugins
```

## Context

```Bash
kubectl config view [--minify]
```