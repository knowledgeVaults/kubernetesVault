# Kubernetes: ConfigMaps

ConfigMaps are objects in Kubernetes used to store non-confidential configuration data in key-value pairs separately from application code

* Allows users to inject configuration into containers without rebuilding images
* Externalizes configurations so that containers can stay immutable
* Consumed by Pods to provide configuration without baking it into container images

<br>

# ConfigMap Injection Methods

## Inside a Container

