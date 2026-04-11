**Selectors** are queries that find Kubernetes objects based on their labels, used to target pods by Services, Deployments, ReplicaSets, Jobs, and NetworkPolicies

## Equality-Based Selectors

The simplest form that matches objects where the label key equals the specified value

```YAML
selector:
  app: my-app
  env: production
```

Used by: `Service.spec.selector`, `ReplicationController`

## Set-Based Selectors (matchExpressions)

More expressive since it supports `In`, `NotIn`, `Exists`, `DoesNotExist` operators

```YAML
selector:
  matchLabels:
    app: my-app
  matchExpressions:
    - key: env
      operator: In
      values: [production, staging]
    - key: deprecated
      operator: DoesNotExist
    - key: tier
      operator: Exists
```

Used by: [[Deployments]], [[ReplicaSets]], [[Daemon Sets]], [[Stateful Sets]], `Job`, [[Network Policies]]

## [[kubectl]] Label Filtering

```Bash
# Single label equality
kubectl get pods --selector app=my-app
kubectl get pods -l app=my-app

# Multiple labels (AND logic)
kubectl get pods -l app=my-app,env=production

# Set-based with In
kubectl get pods -l 'env in (production, staging)'

# Existence check
kubectl get pods -l 'app'

# Absence check
kubectl get pods -l '!deprecated'

# NotIn
kubectl get pods -l 'env notin (dev, test)'
```

## Node Selectors

`nodeSelector` in a Pod spec uses equality-based selectors to constrain scheduling to nodes with matching labels

```YAML
spec:
  nodeSelector:
    kubernetes.io/arch: amd64
    disktype: ssd
```

```Bash
# Label a node
kubectl label node NODE_NAME disktype=ssd

# Verify placement
kubectl get pods -o wide
```

## Field Selectors

Field selectors filter by object metadata fields (not labels)

```Bash
kubectl get pods --field-selector status.phase=Running
kubectl get pods --field-selector spec.nodeName=worker-1
kubectl get pods --field-selector metadata.namespace=default,status.phase=Pending
```

## Selector Matching in Services

A Service without a selector (no `spec.selector`) does not automatically create `Endpoints` since you must manage them manually or use `ExternalName`. This pattern is useful for routing to external databases or cross-cluster services.
