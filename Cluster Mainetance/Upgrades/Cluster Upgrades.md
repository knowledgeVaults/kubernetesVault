
* None of the components should be running on a version that's higher than the [[kube-apiserver]] (It's not mandatory for all Kubernetes cluster components to have the same version)
* Kubernetes usually only supports up to the recent three minor versions (ex: for Kubernetes v1.13 only v1.12 and v1.11 are supported)
* The best approach is to upgrade from one minor version at a time

## [[Cluster Upgrades]] Steps

1. Upgrade the Master Nodes
	* The applications should still be able to serve traffic if the worker nodes and pods are still running)
	
2. Upgrade the Worker Nodes (You can either upgrade all worker nodes, upgrade one worker node at a time or add new nodes running newer versions)

## Upgrading Clusters via [[kubeadm]]

Upgrade kube-apiserver version
```Bash
#
kubeadm upgrade plan

#
apt-get upgrade -y kubeadm=VERSION
kubeadm upgrade apply VERSION
```

Upgrade the [[kubelet]] version
```Bash
# Drain the node first
kubectl drain NODE_NAME

# Upgrade the kubelet version
apt-get upgrade -y kubelet=VERSION
apt-get upgrade node config --kubelet-version KUBELET_VERSION
systemctl restart kubelet

# 
kubectl uncordon NODE_NAME
```

## tmp

View cluster's current version
```Bash
kubectl get nodes
```

List safe versions to upgrade
```Bash
kubeadm upgrade plan
```

Upgrade control plane components 
```Bash
kubeadm upgrade apply VERSION [-y]
```

Upgrade kubeadm
```Bash
sudo apt -y install kubeadm=TARGET_VERSION
```

## Version Compatibility Rules

* No component should run a version higher than `kube-apiserver`
* `kubelet` can be up to 2 minor versions behind the API server
* Kubernetes supports the 3 most recent minor versions — upgrade one minor version at a time

## Complete Upgrade Sequence (kubeadm)

### Control Plane Node

```Bash
# 1. Upgrade kubeadm
sudo apt-mark unhold kubeadm
sudo apt-get install -y kubeadm=TARGET_VERSION
sudo apt-mark hold kubeadm

# 2. Verify upgrade plan
kubeadm upgrade plan

# 3. Apply upgrade to control plane components
sudo kubeadm upgrade apply TARGET_VERSION

# 4. Drain, upgrade kubelet + kubectl, uncordon
kubectl drain NODE_NAME --ignore-daemonsets
sudo apt-mark unhold kubelet kubectl
sudo apt-get install -y kubelet=TARGET_VERSION kubectl=TARGET_VERSION
sudo apt-mark hold kubelet kubectl
sudo systemctl restart kubelet
kubectl uncordon NODE_NAME
```

### Worker Nodes (repeat per node)

```Bash
# On control plane:
