
**Annotations** are key-value metadata attached to Kubernetes objects that store arbitrary non-identifying information used by tools, controllers, and integrations

* Unlike Labels, annotations are **not** used by Kubernetes selectors to identify or group objects
* Can hold larger or more complex values than labels (e.g., JSON blobs, URLs, timestamps)
* Commonly used by [[Ingress]] controllers, monitoring tools, cert-manager, and CI/CD systems

## Annotation Definition

```YAML
metadata:
  annotations:
    description: "Backend API service for the billing module"
    owner: "platform-team@example.com"
    last-deployed: "2024-06-01T14:00:00Z"
    prometheus.io/scrape: "true"
    prometheus.io/port: "9090"
    kubernetes.io/change-cause: "Bumped image to v2.3.1 for security patch"
```

## Common Built-in Annotations

| Annotation | Set By | Purpose |
|---|---|---|
| `[[kubectl]].kubernetes.io/last-applied-configuration` | `kubectl apply` | Stores last-applied manifest for diff-based updates |
| `kubernetes.io/change-cause` | User | Records the reason for a [[Deployments]] rollout (visible in `kubectl rollout history`) |
| `cluster-autoscaler.kubernetes.io/safe-to-evict` | User | Controls whether the Cluster Autoscaler can evict the pod |
| `prometheus.io/scrape` | User | Signals Prometheus to scrape this pod |

## Annotation Keys

Annotation keys follow the format `prefix/name`

* `prefix`: optional DNS subdomain (e.g., `example.com`); reserved prefixes `kubernetes.io/` and `k8s.io/` are for Kubernetes itself
* `name`: required; max 63 characters; alphanumeric, `-`, `_`, `.`

## Imperative Annotation Commands

```Bash
# Add an annotation
kubectl annotate pod POD_NAME description="my pod"

# Update an annotation (must use --overwrite)
kubectl annotate pod POD_NAME description="updated description" --overwrite

# Remove an annotation
kubectl annotate pod POD_NAME description-

# View all annotations
kubectl get pod POD_NAME -o jsonpath='{.metadata.annotations}'
```

## Annotations vs Labels

| Feature | Labels | Annotations |
|---|---|---|
| Used by selectors | Yes | No |
| Max value size | 63 chars | No enforced limit |
| Purpose | Identification / grouping | Metadata / tooling |
