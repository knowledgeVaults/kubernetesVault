
An **[[ApplicationSets]]** is a custom object used by [[Argo CD]] 

* Doesn't exist in Kubernetes unless Argo CD is installed

## How ApplicationSets Work

An `ApplicationSet` uses **generators** to produce a list of parameters, then instantiates one `Application` per parameter set using a template

```YAML
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: multi-env-apps
  namespace: argocd
spec:
  generators:
    - list:
        elements:
          - cluster: staging
            url: https://staging-cluster.example.com
            namespace: staging
          - cluster: production
            url: https://production-cluster.example.com
            namespace: production
  template:
    metadata:
      name: 'my-app-{{cluster}}'
    spec:
      project: default
      source:
        repoURL: https://github.com/my-org/my-app.git
        targetRevision: main
        path: 'manifests/{{namespace}}'
      destination:
        server: '{{url}}'
        namespace: '{{namespace}}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

## Common Generators

| Generator | Use Case |
|---|---|
| `list` | Explicit list of environments or clusters |
| `cluster` | One Application per registered Argo CD cluster |
| `git` | One Application per directory or file in a Git repo |
| `matrix` | Cartesian product of two other generators |
| `merge` | Combine multiple generators with overrides |

## Commands

```Bash
kubectl get applicationsets -n argocd
kubectl describe applicationset multi-env-apps -n argocd
```

## Benefits of ApplicationSets

* Reduces repetition since it's defined once (You can generate many Application)
* Single commit to Git creates or updates all environments simultaneously
* Cluster-level GitOps: adding a new cluster automatically gets all Applications deployed to it

```Bash
kubectl get applications -n argocd    # See generated Applications
```


## Git Generator Example
```YAML
generators:
  - git:
      repoURL: https://github.com/my-org/apps.git
      revision: HEAD
      directories:
        - path: "apps/*"
```
This creates one Argo CD Application for each directory under `apps/` in the repository.
