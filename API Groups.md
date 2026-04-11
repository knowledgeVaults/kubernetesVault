
**[[API Groups]]** are a way of grouping related resources in the Kubernetes API, allowing independent versioning and enabling extensions without polluting the core API

* The Kubernetes API is served at `/api` (core group) and `/apis` (named groups)
* Each group can have multiple versions (e.g., `v1`, `v1beta1`, `v2`)
* CRDs add new groups dynamically without modifying Kubernetes itself

## API Endpoint Categories

| Endpoint | Purpose |
|---|---|
| `/api` | Core API group (Pods, Services, ConfigMaps, etc.) |
| `/apis` | Named API groups (apps, batch, networking.k8s.io, etc.) |
| `/version` | Server version info |
| `/healthz` | [[kube-apiserver]] health check |
| `/metrics` | Prometheus metrics |
| `/logs` | Log streaming endpoint |
| `/openapi/v2` | OpenAPI schema for all API resources |

## Core API Group (`/api`)

The core group has no group name — resources use `apiVersion: v1`

```
/api/v1/pods
/api/v1/services
/api/v1/namespaces
/api/v1/nodes
/api/v1/persistentvolumes
```

## Named API Groups (`/apis`)

Named groups use `apiVersion: GROUP/VERSION`

| Group | Version | Resources |
|---|---|---|
| `apps` | `v1` | Deployments, ReplicaSets, StatefulSets, DaemonSets |
| `batch` | `v1` | Jobs, CronJobs |
| `networking.k8s.io` | `v1` | [[Ingress]], [[Network Policies]] |
| `[[RBAC]].authorization.k8s.io` | `v1` | Roles, ClusterRoles, RoleBindings |
| `storage.k8s.io` | `v1` | [[Storage Classes]], VolumeAttachment |
| `autoscaling` | `v2` | [[Horizontal Pod Autoscaler]] |
| `policy` | `v1` | [[PodDisruptionBudgets]] |
| `coordination.k8s.io` | `v1` | Lease |
| `discovery.k8s.io` | `v1` | [[Endpoint Slices]] |

## Discovering Available APIs

```Bash
kubectl api-resources                    # All available resource kinds
kubectl api-resources --namespaced=true  # Namespace-scoped only
kubectl api-versions                     # All available GROUP/VERSION combinations
kubectl explain pod                      # Field documentation for a resource
kubectl explain pod.spec.containers      # Nested field documentation
```

## Accessing the API Directly

```Bash
kubectl proxy &
curl http://localhost:8001/apis          # List all API groups
curl http://localhost:8001/api/v1/pods  # List all pods (cluster-wide)
```
