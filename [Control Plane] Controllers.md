# Kubernetes: Controllers

Controllers in Kubernetes are control loops that continuously monitor the state of the cluster to ensure that the cluster's current state aligns with the user's desired state

* Each controller tracks at least one Kubernetes resource type
* It watches the `specs` from your YAML files via the API server and automatically reconciles them

<br>

# Controller Types

## Build-In Controllers

Built-in controllers are automatically dpeloyed as part of Kubernetes core components

* No need to manually define and deploy these controllers
* They run inside the kube-controller-manager process 

<br>

## Custom Controllers

* Requires users to build, containerize, and deploy them as pods and define any CRDs