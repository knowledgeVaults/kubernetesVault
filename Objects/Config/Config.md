The **[[kubeconfig]]** file (`~/.kube/config`) is the configuration file used by `[[kubectl]]` and other Kubernetes clients to connect to clusters and authenticate

* Contains cluster connection details, user credentials, and context bindings
* Multiple clusters, users, and contexts can coexist in a single file
* The `current-context` field determines which cluster/namespace/user is active by default

## kubeconfig Structure

```YAML
apiVersion: v1
kind: Config
current-context: dev-context

clusters:
  - name: my-cluster
    cluster:
      server: https://APISERVER:6443
      certificate-authority-data: BASE64_ENCODED_CA

users:
  - name: alice
    user:
      client-certificate-data: BASE64_ENCODED_CERT
      client-key-data: BASE64_ENCODED_KEY
  - name: service-account-user
    user:
      token: BEARER_TOKEN

contexts:
  - name: dev-context
    context:
      cluster: my-cluster
      user: alice
      namespace: development
```

## Context Management

A context binds a cluster, a user, and an optional default namespace together

```Bash
kubectl config get-contexts                # List all contexts
kubectl config current-context             # Show active context
kubectl config use-context CONTEXT_NAME    # Switch context

# Set default namespace for current context
kubectl config set-context --current --namespace=production

# Create a new context
kubectl config set-context new-context --cluster=my-cluster --user=bob --namespace=staging
```

## Merging kubeconfigs

Multiple kubeconfig files can be merged using the `KUBECONFIG` environment variable

```Bash
export KUBECONFIG=~/.kube/config:~/.kube/config-cluster2
kubectl config view --flatten > ~/.kube/merged-config
```

## Viewing and Editing

```Bash
kubectl config view                       # Show full config (certificates redacted)
kubectl config view --minify              # Show only active context's config
kubectl config view --raw                 # Show with raw certificate data

# Delete a context
kubectl config delete-context OLD_CONTEXT
kubectl config delete-cluster OLD_CLUSTER
```
