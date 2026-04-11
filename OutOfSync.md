# Out of Sync Resources

This note documents **OutOfSync** states in the context of GitOps tools like [[Argo CD]] when the actual cluster state diverges from the desired state defined in Git

## What OutOfSync Means in Argo CD

An Argo CD Application is `OutOfSync` when the live Kubernetes resources differ from the manifests stored in the Git source

* Detected by comparing a diff of the live state (from the Kubernetes API) against the rendered manifests from Git
* Common causes: manual [[kubectl]] changes, automated platform updates, or untracked resources

## Common Causes

| Cause              | Example                                           |
| ------------------ | ------------------------------------------------- |
| Manual edit        | `kubectl edit Deployments my-app` changed a field |
| Automated mutation | Admission webhook injected a sidecar not in Git   |
| Image tag drift    | Image was updated outside Git by a CI job         |
| Missing resource   | Resource in Git doesn't exist in cluster          |
| Extra resource     | Resource exists in cluster but not in Git         |

## Resolving OutOfSync

```Bash
# Sync to bring cluster back in line with Git
argocd app sync APP_NAME

# Sync with pruning (delete resources removed from Git)
argocd app sync APP_NAME --prune

# Force sync with server-side apply
argocd app sync APP_NAME --server-side --force
```

## Preventing Manual Drift

Enable `selfHeal` in the Application's sync policy

```YAML
syncPolicy:
  automated:
    prune: true
    selfHeal: true     # Automatically revert any manual changes
```

With `selfHeal: true`, Argo CD continuously reconciles the cluster state toward Git where any manual change is reverted within minutes.

## Ignoring Expected Differences

Some fields change legitimately and should not be flagged as OutOfSync

```YAML
spec:
  ignoreDifferences:
    - group: apps
      kind: Deployment
      jsonPointers:
        - /spec/replicas    # HPA manages replicas; ignore Git value
```

## Related: Argo CD Sync Phases

Argo CD syncs resources in ordered phases via sync waves (`argocd.argoproj.io/sync-wave` annotation). Resources in lower waves sync before higher waves, allowing dependencies (e.g., CRDs before CRs) to be established first.
