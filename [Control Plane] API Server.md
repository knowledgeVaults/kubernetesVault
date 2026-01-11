# Kubernetes: API Server

The API Server in a Kubernetes is the central entry point of the Kubernetes control plane

* All communication within a Kubernetes cluster passes through the API server

![](https://github.com/knowledgeVaults/imagesVault/blob/main/kubernetes-api-server.png)

<br>

# API Server Fonctionalities

* Exposes the Kubernetes API over HTTPS
* Provides authentication (Who the user is) and authorization (What the user is allowed to do)
* The only component that talks directly to etcd