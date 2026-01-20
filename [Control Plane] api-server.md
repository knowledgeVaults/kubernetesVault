# Kubernetes: API Server

The API Server in a Kubernetes is the central entry point of the Kubernetes control plane

* All communication within a Kubernetes cluster passes through the API server
* Runs on port 6443 

![](https://raw.githubusercontent.com/knowledgeVaults/imagesVault/main/kubernetes-api-server.png)

<br>

# API Server Fonctionalities

* Exposes the Kubernetes API over HTTPS
* Provides authentication (Who the user is) and authorization (What the user is allowed to do)
* The only component that talks directly to etcd

<br>

# API Groups
