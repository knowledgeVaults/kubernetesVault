
**Labels** are key-value pairs attached to Kubernetes objects that are used for identification, grouping, and selection

* The primary mechanism for selectors (Services, Deployments, ReplicaSets, and NetworkPolicies all use labels to target pods)
* Should be applied consistently and meaningfully to enable effective filtering, querying, and automation

## Label Definition

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
    env: production
    version: v2
    tier: backend
spec:
  containers:
    - name: app
      image: my-app:v2
```

## Label Key Format

Label keys follow `prefix/name` format:
* `prefix`: optional DNS subdomain (max 253 chars); `kubernetes.io/` and `k8s.io/` are reserved
* `name`: required; max 63 characters; alphanumeric, `-`, `_`, `.`

## Recommended Label Set (Kubernetes Best Practices)

| Label | Example Value | Purpose |
|---|---|---|
| `app.kubernetes.io/name` | `my-app` | Application name |
| `app.kubernetes.io/version` | `v2.1.0` | Application version |
| `app.kubernetes.io/component` | `backend` | Component role |
| `app.kubernetes.io/part-of` | `billing-system` | Parent application |
| `app.kubernetes.io/managed-by` | `[[Helm]]` | Tool managing the object |

## Label Commands

```Bash
# Add a label
kubectl label pod POD_NAME env=staging

# Update a label (requires --overwrite)
kubectl label pod POD_NAME env=production --overwrite

# Remove a label
kubectl label pod POD_NAME env-

# Filter by label
kubectl get pods -l app=my-app
kubectl get pods -l 'env in (production, staging)'
kubectl get pods -l 'env notin (dev)'
kubectl get pods -l app=my-app,env=production
```

## Equality-Based vs Set-Based Selectors

```YAML
# Equality-based (simple key=value)
selector:
  matchLabels:
    app: my-app

# Set-based (supports In, NotIn, Exists, DoesNotExist)
selector:
  matchExpressions:
    - key: env
      operator: In
      values: [production, staging]
    - key: deprecated
      operator: DoesNotExist
```

## Label Immutability on Selectors

Once a `matchLabels` selector is set on a [[Deployments]] or [[ReplicaSets]], it **cannot be changed** since it is immutable after creation. Changing the selector requires deleting and recreating the resource.
