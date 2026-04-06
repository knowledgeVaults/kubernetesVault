
The `kubelet` is the primary node agent that runs on every worker node in a Kubernetes cluster and acts as the bridge between control plane decisions and actual execution on nodes

* Automatically registers the node with the API server
* Ensures containers in assigned pods are running and healthy via liveness and readiness probes
* Ensures that the objects on its node are running in its desired state by reconciling PodSpecs received from the API server when any changes are made
* Manages pod lifecycles using the container runtime (*Start*, *Stop*, *Restart*, *Eviction*)
* Reports pod and node statuses back to the control plane via port 10250
* Enforces CPU and memory limits when specified 
* Mounts volumes using CSI drivers
* Injects ConfigMaps and Secrets into containers when specified
* Works only at the Pod level

```Text
kubelet --> Authentication (ex: Kubeconfig) --> Authorization (ex: RBAC) --> Admission Controllers --> Operation (ex: Deploy Pods)
```