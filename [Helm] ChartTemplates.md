# Kubernetes: Helm Chart Templates

Helm templates are standard Kubernetes YAML files enhanced with Go templating syntax

<br>

# Template Rendering Process

Helm will process all the files under `templates/` together by performing the following steps:

1. Load the values from `values.yaml` and any `-f` overrides
2. Apply Go template engine to inject values, evaluate helpers, and handle logic
3. Output the final Kubernetes-valid YAML applied to the cluster

<br>

# Template Files

## chart.yaml

## values.yaml

values.yaml provides default configuration values for Helm charts in Kubernetes

* Enables parameterized and reusable deployments
* Acts as the central place to define settings that get injected into chart templates during rendering