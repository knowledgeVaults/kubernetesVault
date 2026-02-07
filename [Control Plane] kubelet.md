# Kubernetes: kubelet

The kubelet is the node agent that runs on every worker and master node within a Kubernetes cluster that ensures that the pods that are supposed to run on its node are actually running and healthy

* Runs on port TCP/10250
* Talks to the API server to ensure that the pods are in their desired state by comparing their actual state with their PodSpecs
* Starts, stops, and restarts containers
* Registers the worker node with the cluster and reports the heartbeats and resource capabity information to the control plane
* Pulls images
* Mounts any specified volumes, ConfigMaps, and Secrets for pods before starting containers 
* Enforces resource limits and cgroups locally
* Looks in the CNI config directory for which plugin to use 
