# Kubernetes: Sidecars Containers

A sidecar container is an additional container that runs in the same Pod as the main application container and provides supporting functionality (*Logging*, *monitoring*, *proxying*, *etc.*)

* Run in the same pod as the main application
* Shares the network namespace and attached volumes of the main application 
