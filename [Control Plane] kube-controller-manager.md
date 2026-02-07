# Kubernetes: kube-controller-manager 

The kube-controller-manager is responsible for embedding control loops that watch the API server for changes in resources in order to help maintain the cluster's desired state

* Takes corrective action in order to reach and maintain the cluster's desired state (*Scaling replicas*, *Evicting pods*, *Allocating pod CIDRs*, *etc.*)
* Runs on port 10257
* Starts automatically when you bootstrap a cluster