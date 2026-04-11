
[[minikube]] is a tool that runs a single-node Kubernetes cluster locally for development and testing

* Deploys a Kubernetes cluster inside a VM, container, or directly on bare metal on your local machine
* Supports multiple drivers: Docker, VirtualBox, Hyper-V, KVM, Podman, and more
* Includes addons for commonly needed components like [[Ingress]], metrics-server, and dashboard

## Installation

```Bash
# macOS
brew install minikube

# Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
install minikube-linux-amd64 /usr/local/bin/minikube
```

## Basic Commands

```Bash
# Start a cluster
minikube start
minikube start --driver=docker --memory=4096 --cpus=2
minikube start --kubernetes-version=v1.29.0

# Stop the cluster
minikube stop

# Delete the cluster
minikube delete

# Check status
minikube status

# Get cluster IP
minikube ip

# SSH into the minikube node
minikube ssh

# Access cluster dashboard
minikube dashboard
```

## Managing Addons

```Bash
minikube addons list
minikube addons enable ingress
minikube addons enable metrics-server
minikube addons enable registry
minikube addons disable dashboard
```

## Working with Images

```Bash
# Use minikube's Docker daemon (avoid push/pull overhead)
eval $(minikube docker-env)
docker build -t my-app:latest .

# Or load a local image into minikube
minikube image load my-app:latest
```

## Multi-Node Clusters

```Bash
minikube start --nodes=3 -p my-multinode-cluster
kubectl get nodes
```

## Port Forwarding via Service Tunnel

```Bash
minikube service SERVICE_NAME --url
# Returns a localhost URL to access the service from outside the cluster
```

## Profiles

```Bash
minikube start -p cluster2          # Create a named profile
minikube profile list               # List all profiles
kubectl config use-context cluster2 # Switch to a minikube profile
```

## Debugging

```Bash
minikube logs                    # View minikube logs
minikube ssh -- systemctl status kubelet
kubectl get events --all-namespaces
```
