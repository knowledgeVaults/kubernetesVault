
[[kubeconfig]] is a YAML configuration file that stores details for accessing Kubernetes cluster (*Authentication credentials*, *Cluster endpoints*, *Cluster context*, *etc.*)

* Enables [[kubectl]] to connect to one or multiple clusters by defining the clusters, users, and contexts 
* Located at `$HOME/.kube/config` by default (You can specify which kubeconfig file to use with the `--kubeconfig=FILE_PATH` flag)
* Worker node components use it for [[kube-apiserver]] communication
* Multiple clusters along with corresponding users and contexts for easy switching can be defined in a single `kubeconfig` file

## kubectl

### View and List

Display merged `kubeconfig` settings
```Bash
kubectl config view
```

Show active context
```Bash
kubectl config current-context
```

List defined clusters
```Bash
kubectl config get-clusters
```

List contexts
```Bash
kubectl config get contexts
```

List users
```Bash
kubectl config get-users
```

### Set and Edit

Add/update cluster
```Bash
kubectl config set-cluster CLUSTER_NAME --server=CLUSTER_URL --certificate-authority=PATH
```

Add/update user
```Bash
kubectl config set-credentials CLUSTER_NAME --client-certificate=PATH --client-key=PATH
```

Add/update cluster context
```Bash
kubectl config set-context CLUSTER_NAME --cluster=CLUSTER --user=USER --namespace=NAMESPACE
```

Switch active context
```Bash
kubectl config set current-context CONTEXT_NAME
```
```Bash
kubectl config use-context CONTEXT_NAME
```

General setter
```Bash
kubectl config set SECTION.FIELD VALUE
```

### Config File Manipulation

Merge multiple config files into the default config file
```Bash
KUBECONFIG=~/.kube/config:PATH_TO_CONFIG kubectl config view --flatten > ~/.kube/config
```

Temporarily load multiple `kubeconfig` files for `kubectl` commands
```Bash
export KUBECONFIG=PATH_TO_CONFIG_1:PATH_TO_CONFIG_2
```

### Delete and Rename

Delete cluster context entry from `kubeconfig`
```Bash
kubectl config delete-cluster CLUSTER_NAME
```

Delete context entry from `kubeconfig`
```Bash
kubectl config delete-context CONTEXT_NAME
```

Delete user entry from `kubeconfig`
```Bash
kubectl config delete-user USER
```

Rename context
```Bash
kubectl config rename-context OLD_NAME NEW_NAME
```

Clear entry value
```Bash
kubectl config unset SECTIO.FIELD
```

## Kubeconfig YAML Template

```YAML
apiVersion: v1
kind: Config
preferences: {}
clusters:
- cluster:
    certificate-authority-data: <BASE64_CA_DATA>  # Base64-encoded CA cert
