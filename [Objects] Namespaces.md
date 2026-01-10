# Kubernetes: Namespaces

A **namespace** is an object that logically groups and isolates resources within a single K8 cluster

* Provides a way to divide a cluster between multiple users or applications

![](https://github.com/knowledgeVaults/imagesVault/blob/main/kubernetes-namespaces.png)

<br>

# Namespace Types

* **default**: The default namespace that's used when no other namespace is specified
* **kube-system**: For Kubernetes system components
* **kube-public**: For publicly readable namespaces
* **kube-node-lease**: For node heartbeats and lease objects
* user-defined: A custom namespace created by a user

<br>

# Common Namespace Deployment Methods

## Method 1

A namespace can be created and deployed using `kubectl`
```Bash
kubectl create namespace NAMESPACE_NAME
```

<br>

## Method 2

A namespace can be defined using a YAML file and then deployed using `kubectl`
```YAML
apiVersion: v1
kind: Namespace
metadata:
  name: NAMESPACE_NAME
  labels:
    name: NAMESPACE_NAME
```
```Bash
kubectl apply -f FILENAME.yaml
```