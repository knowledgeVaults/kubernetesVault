
An object in Kubernetes is a persistent "record of intent" that tells the cluster your desired state 

* Exists as a cluster-wide API resource that's managed by the control plan and stored in [[etcd]] via the [[kube-apiserver]]

## Object Lifecycle

Objects are managed using controllers

* Controllers handle creation, updates, scaling, and deletion of all objects via the API server
* The [[kubelet]] watches the API server for Pod objects assigned to its node and acts on them 

## Object Structure

Objects are typically defined using either YAML or JSON

### YAML Example

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: POD_NAME
spec:
  containers:
  - name: CONTAINER_NAME
    image: IMAGE:TAG
```

### JSON Example

```JSON
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": { "name": "POD_NAME" },
  "spec": {
    "containers": [{ "name": "CONTAINER_NAME", "image": "IMAGE:TAG" }]
  }
}

```

## Kubernetes Objects

### Workload 

* [[Pods]]: The smallest deployable object in a Kubernetes cluster running one or more containers sharing network and storage
* [[kubernetesVault/Objects/Deployments/Deployments|Deployments]]: Controllers that manage ReplicaSets to deploy, scale, and update stateless Pods

### Configuration

* [[Config Maps]]: Stores non-confidential configuration data as either key-value pairs in plaintext or binary data
* [[kubernetesVault/Objects/Secrets/Secrets|Secrets]]: Stores encoded sensitive data for secure Pod access

### Storage

* [[PersistentVolumes]]: Cluster-wide storage resources provisioned independently of Pod lifecycles
* [[PersistentVolumeClaims]] Namespaces requests for storage that bind to available PersistentVolumes

### Service Discovery

* [[kubernetesVault/Objects/Services/Services|Services]]: Abstractions providing stable endpoints and load balancing for dynamic pods
* [[Ingress]]: Defines rules for routing external HTTP/HTTPS traffic to Services

## Scopes

### Namespaced

These objects only exist inside a specific namespace

```Bash
# List all namespaced objects
kubectl api-resources --namespaced=true
```

*pods*, *replicasets*, *deployments*, *PVCs*, *etc.*

### Cluster Scoped

These objects exist across the entire cluster

```Bash
# List all cluster scoped objects
kubectl api-resrouces --namespaces=false
```

*nodes*, *PVs*, *clusterroles*, *clusterrolebindings*, *certificatesigningrequests*, *namespaces*, *etc.*