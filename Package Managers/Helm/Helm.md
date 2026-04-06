
* Makes use of go templates to allow assigning variables to properties
* Helm is a complete package manager for an application rather than just a tool to customize configurations on a per environment basis
* Helm templates are not valid YAML as they use go templating syntax

Install helm
```Bash
sudo snap install helm --classic
```

## Helm Versions

### Helm 1

### Helm 2

* Has Tiller

### Helm 3

* Removed Tiller with RBAC
* Revisions work differently in Helm 3

## Helm Components

### Helm Command-Line Utility

```Bash
helm --help
```

### Helm Charts

* Contains all the instructions that Helm needs to be able to create the objects 

```
chart
|--> templates # Template directory
    |--> values.yaml
    |--> chart.yaml
    |--> LICENSE
    |--> README.md
| --> charts # Dependency charts
```

#### values.yaml

* The only file you'll need to modify to customer the deployment of the application

#### chart.yaml

* Contains information about the chart itself

## Chart Hooks

### Release

* A single instance of an application using a Helm chart
* Each release can have multiple revisions
* Each release can be managed independently even if they're from the same chart

### Revisions

* Each revision is like a snapshot of the application
* Every time a change is made to the application, a new revision is made

### Metadata

* Saves the metadata in your Kubernetes cluster as Secrets (This allows the data to survive and as long as the Kubernetes cluster survives then everyone from your team can access it)
* Allows Helm to know what it did in the cluster since it still has its metadata available

## Lifecycle Management

* Managed through releases

```Bash
helm history RELEASE_NAME
```

```Bash
helm rollback RELEASE_NAME
```