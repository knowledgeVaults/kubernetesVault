
* None of the components should be running on a version that's higher than the kube-apiserver (It's not mandatory for all Kubernetes cluster components to have the same version)
* Kubernetes usually only supports up to the recent three minor versions (ex: for Kubernetes v1.13 only v1.12 and v1.11 are supported)
* The best approach is to upgrade from one minor version at a time

## Cluster Upgrade Steps



### 1. Upgrade the Master Nodes

* The applications should still be able to serve traffic if the worker nodes and pods are still running

### 2. Upgrade the Worker Nodes

### Upgrade All Worker Nodes

### Upgrade One Worker Node at a Time

### Add New Nodes Running Newer Versions

* Once the new nodes are added you can then decommission the old nodes

## Upgrading Clusters via kubeadm

Upgrade kube-apiserver version
```Bash
#
kubeadm upgrade plan

#
apt-get upgrade -y kubeadm=VERSION
kubeadm upgrade apply VERSION
```

Upgrade the kubelet version
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