
**[[Argo CD]]** is a declarative, GitOps-based continuous delivery tool for Kubernetes that automatically syncs cluster state from a Git repository

* Runs as a set of controllers and API servers inside the cluster
* Monitors Git repositories for changes and applies them to target clusters
* Supports multiple manifest formats: plain YAML, [[Helm]] charts, [[Kustomize]] overlays, Jsonnet, and custom plugins

## Core Concepts

| Concept | Description |
|---|---|
| **Application** | The fundamental Argo CD unit — maps a Git source to a Kubernetes destination |
| **Project** | Groups applications; defines allowed sources, destinations, and access controls |
| **Sync** | The act of applying Git-defined state to the cluster |
| **Health** | Status of Kubernetes resources (Healthy, Degraded, Progressing, Missing) |
| **OutOfSync** | Cluster state differs from Git-defined desired state |

## Application Manifest

```YAML
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/my-org/my-app.git
    targetRevision: main
    path: manifests/production
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true        # Delete resources removed from Git
      selfHeal: true     # Revert manual changes to cluster
    syncOptions:
      - CreateNamespace=true
```

## Sync Policies

| Policy | Behaviour |
|---|---|
| Manual | Sync only when triggered via CLI or UI |
| Automated | Automatically sync when Git changes are detected |
| `prune: true` | Delete cluster resources not in Git |
| `selfHeal: true` | Automatically revert out-of-band changes |

## Installation

```Bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

## Application Health States

`Healthy` → `Progressing` → `Healthy` (after successful rollout)  
`Degraded` → pods are crashing or rollout failed
