
**[[Helm]]** is the package manager for Kubernetes where it packages, distributes, and manages Kubernetes applications as versioned, templated bundles called **Charts**

* Uses Go templates to parameterise YAML manifests, enabling reuse across environments
* Tracks releases as versioned revisions, supporting upgrade, rollback, and history
* Charts can declare dependencies on other charts, forming composable application packages

## Helm Components

| Component | Description |
|---|---|
| **Chart** | A bundle of YAML templates + a `values.yaml` + `Chart.yaml` metadata |
| **Release** | A deployed instance of a chart in a cluster; each install is a separate release |
| **Revision** | A snapshot of a release; every install/upgrade creates a new revision |
| **Repository** | A collection of charts hosted over HTTP |

## Chart Structure

```
my-chart/
├── Chart.yaml          # Chart metadata (name, version, description)
├── values.yaml         # Default configurable values
├── templates/          # Go-templated YAML manifests
│   ├── deployment.yaml
│   ├── service.yaml
│   └── _helpers.tpl    # Template helper functions
└── charts/             # Dependency sub-charts
```

## Core Commands

```Bash
# Add a repository
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Search for charts
helm search repo nginx
helm search hub wordpress

# Install a chart
helm install my-release bitnami/nginx
helm install my-release bitnami/nginx --set replicaCount=3 -f custom-values.yaml

# Upgrade a release
helm upgrade my-release bitnami/nginx --set image.tag=1.25

# View release history
helm history my-release

# Rollback to previous revision
helm rollback my-release 1

# Uninstall
helm uninstall my-release

# Dry-run (render templates without applying)
helm install my-release bitnami/nginx --dry-run --debug
helm template my-release bitnami/nginx -f values.yaml
```

## values.yaml Override Precedence

```
Chart defaults → -f values-file → --set KEY=VALUE (highest priority)
```
