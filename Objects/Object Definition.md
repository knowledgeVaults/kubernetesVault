
Every Kubernetes object is defined by a YAML or JSON manifest with four top-level fields: `apiVersion`, `kind`, `metadata`, and `spec`

## Top-Level Structure

```YAML
apiVersion: GROUP/VERSION   # e.g., apps/v1, v1, batch/v1
kind: RESOURCE_KIND         # e.g., Pod, Deployment, Service
metadata:
  name: OBJECT_NAME
  namespace: NAMESPACE       # Omit for cluster-scoped resources
  labels:
    KEY: VALUE
  annotations:
    KEY: VALUE
spec:
  # Resource-specific desired state
```

## apiVersion

Identifies which API group and version handles this resource

| apiVersion                     | Resources                                                            |
| ------------------------------ | -------------------------------------------------------------------- |
| `v1`                           | Pod, Service, [[Config Maps]], [[Secrets]], Namespace, PV, PVC       |
| `apps/v1`                      | [[Deployments]], [[ReplicaSets]], [[Stateful Sets]], [[Daemon Sets]] |
| `batch/v1`                     | Job, CronJob                                                         |
| `networking.k8s.io/v1`         | [[Ingress]], [[Network Policies]]                                    |
| `RBAC.authorization.k8s.io/v1` | Role, [[Cluster Roles]], [[RoleBindings]], [[Cluster Role Bindings]] |
| `storage.k8s.io/v1`            | [[Storage Classes]]                                                  |
| `autoscaling/v2`               | [[Horizontal Pod Autoscaler]]                                        |
| `policy/v1`                    | [[PodDisruptionBudgets]]                                             |

## metadata

Stores identifying and organisational information about the object

```YAML
metadata:
  name: my-deployment             # Required; unique within namespace
  namespace: production           # Required for namespaced resources
  labels:
    app: my-app
    env: production
  annotations:
    description: "Main backend API"
  ownerReferences:                # Set automatically for owned objects (pods by RS, etc.)
    - apiVersion: apps/v1
      kind: ReplicaSet
      name: my-deployment-abc123
      uid: UUID
      controller: true
```

## spec

Defines the desired state where the schema varies per resource type

```YAML
# Pod spec example
spec:
  containers:
    - name: CONTAINER_NAME
      image: IMAGE:TAG
      ports:
        - containerPort: 8080
      resources:
        requests:
          cpu: "100m"
          memory: "64Mi"
```

## status

The `status` field is populated and managed by Kubernetes and is never set manually in a manifest

```Bash
kubectl get pod POD_NAME -o yaml | grep -A10 "^status:"
```
