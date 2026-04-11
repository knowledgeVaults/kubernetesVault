
```Bash
curl -sSL -o argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x argocd
sudo mv argocd /usr/local/bin
argocd version
```

## Common argocd CLI Commands

```Bash
# Login
argocd login localhost:8080 --username admin --password PASSWORD --insecure

# List applications
argocd app list

# Get app status
argocd app get APP_NAME

# Sync an application manually
argocd app sync APP_NAME

# Sync and wait for completion
argocd app sync APP_NAME --timeout 300

# Rollback to previous revision
argocd app rollback APP_NAME

# Delete an application
argocd app delete APP_NAME

# Diff: show what would change on next sync
argocd app diff APP_NAME

# Force hard refresh (bypass cache)
argocd app get APP_NAME --refresh

# Create application from CLI
argocd app create my-app \
  --repo https://github.com/my-org/my-app.git \
  --path manifests/production \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace production \
  --sync-policy automated

# List all projects
argocd proj list

# List all clusters
argocd cluster list
```


## Getting the Initial Admin Password

```Bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```

## Checking Sync Status

```Bash
argocd app get APP_NAME    # Shows sync status, health, and last sync time
argocd app history APP_NAME
```


## Syncing with Pruning

```Bash
# Sync and prune resources removed from Git
argocd app sync APP_NAME --prune

# Force sync even if already synced
argocd app sync APP_NAME --force
```
## Server-Side Apply

```Bash
argocd app sync APP_NAME --server-side
```
Server-side apply avoids annotation size limits and handles field manager conflicts better than client-side apply.


## Registering an External Cluster

```Bash
# Add a cluster context to Argo CD
argocd cluster add CONTEXT_NAME
argocd cluster list
```
The cluster is stored as a [[Secrets]] in the `argocd` namespace.


## App of Apps Pattern

Create a root Application that manages other Applications from a Git repo as this bootstraps the entire GitOps hierarchy from a single `argocd app create` command.
