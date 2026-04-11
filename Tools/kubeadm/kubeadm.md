
[[kubeadm]] is the official Kubernetes cluster bootstrapping tool that automates the installation and configuration of a single-master or multi-master Kubernetes cluster

* Handles certificate generation, static pod manifests, [[kubeconfig]] files, and cluster token distribution
* Does not manage infrastructure (node provisioning) since it configures the Kubernetes layer on already-running machines
* Works alongside [[kubectl]] and [[kubelet]]; all three are typically installed from the same package source

## Bootstrapping a Cluster

### 1. Initialize the Control Plane

Run on the first master node

```Bash
kubeadm init \
  --pod-network-cidr=10.244.0.0/16 \   # Must match CNI plugin requirements
  --apiserver-advertise-address=MASTER_IP \
  --control-plane-endpoint=LOAD_BALANCER_DNS   # For HA setups
```

### 2. Configure kubectl

```Bash
mkdir -p $HOME/.kube
cp /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
```

### 3. Install a CNI Plugin

```Bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

### 4. Join Worker Nodes

```Bash
# Get the join command (from kubeadm init output)
kubeadm token create --print-join-command

# Run on each worker node
kubeadm join MASTER_IP:6443 --token TOKEN --discovery-token-ca-cert-hash sha256:HASH
```

## [[Cluster Upgrades]]

```Bash
# 1. Upgrade kubeadm
apt-get update && apt-get install -y kubeadm=TARGET_VERSION

# 2. Plan and apply upgrade on control plane
kubeadm upgrade plan
kubeadm upgrade apply TARGET_VERSION

# 3. Drain node, upgrade kubelet, uncordon
kubectl drain NODE_NAME --ignore-daemonsets
apt-get install -y kubelet=TARGET_VERSION kubectl=TARGET_VERSION
systemctl restart kubelet
kubectl uncordon NODE_NAME
```

## Useful Commands

```Bash
kubeadm version                  # Show kubeadm version
kubeadm config print init-defaults   # Show default init configuration
kubeadm reset                    # Tear down a cluster (run on all nodes)
kubeadm token list               # List active bootstrap tokens
kubeadm certs check-expiration   # Show certificate expiry dates
kubeadm certs renew all          # Renew all cluster certificates
```
