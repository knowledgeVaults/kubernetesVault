
`Namespaces` are cluster-scoped API resources that are used to logically separate resources inside the same cluster

```YAML
apiVersion: v1
kind: Namespace
metadata:
  name: NAMESPACE_NAME
```

## Default Namespaces

### default

The `default` namespace is used for user workloads that have been created without specifying a namespace

```Bash
kubectl get pods -n default
```

## kube-system

The `kube-system` namespace is used for Kubernetes system components required for the cluster to function (ex: *kube-proxy*, *controller-manager*, *calico*)

```Bash
kubectl get pods -n kube-system
```

### kube-public

The `kube-public` namespace is used for public resources readable by anyone (ex: *ConfigMaps*)

* Rarely used

### kube-node-lease

The `kube-node-lease` namespace is used for node heartbeats

```Bash
kubectl get lease -n kube-node-lease
```

##


List the resources within a namespace
```Bash
kubectl get RESOURCE {-n NAMESPACE|--namespace=NAMESPACE}
```

List all resources within all namespaces
```Bash
kubectl get RESOURCE --all-namespaces
```

Identify the current context and set the namespace to the desired one
```Bash
kubectl config set-context $(kubectl config current-context) --namespace=NAMESPACE
```