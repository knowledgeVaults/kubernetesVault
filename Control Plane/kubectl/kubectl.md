[[kubectl]] is the primary command-line interface tool for interacting with Kubernetes clusters

* Communicates with the [[kube-apiserver]] via HTTPS using credentials from the active [[kubeconfig]] context
* Translates user commands into REST API requests enabling full CRUD operations on Kubernetes objects
* Reads cluster connection details from `~/.kube/config` by default (`KUBECONFIG` env var overrides this)

## kubeconfig Structure

```YAML
apiVersion: v1
kind: Config
clusters:
  - name: my-cluster
    cluster:
      server: https://APISERVER:6443
      certificate-authority-data: BASE64_CA
contexts:
  - name: dev-context
    context:
      cluster: my-cluster
      user: alice
      namespace: development
current-context: dev-context
users:
  - name: alice
    user:
      client-certificate-data: BASE64_CERT
      client-key-data: BASE64_KEY
```

## Context Management

```Bash
kubectl config view                          # Show full kubeconfig
kubectl config get-contexts                  # List all contexts
kubectl config current-context               # Show active context
kubectl config use-context CONTEXT_NAME      # Switch context
kubectl config set-context --current --namespace=NAMESPACE  # Change default namespace
```

## Apply vs Create

```Bash
kubectl apply -f manifest.yaml   # Declarative: create or update (stores last-applied annotation)
kubectl create -f manifest.yaml  # Imperative: create only; fails if resource already exists
```

`kubectl apply` stores the submitted configuration in the `kubectl.kubernetes.io/last-applied-configuration` annotation. On subsequent applies, Kubernetes diffs the new config against the last-applied config to determine what to remove.

## Output Formats

```Bash
kubectl get pods -o wide          # Node IP, pod IP, node name
kubectl get pods -o yaml          # Full YAML representation
kubectl get pods -o json          # Full JSON representation
kubectl get pods -o name          # Resource/name only
kubectl get pods -o jsonpath='{.items[0].metadata.name}'
kubectl get nodes -o custom-columns=NAME:.metadata.name,CPU:.status.capacity.cpu
```

## Dry Run

```Bash
kubectl apply -f manifest.yaml --dry-run=client   # Validate locally
kubectl apply -f manifest.yaml --dry-run=server   # Validate against API server
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
```
