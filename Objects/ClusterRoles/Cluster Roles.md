A **[[Cluster Roles]]** is an [[RBAC]] object that defines cluster-scoped permissions for granting access to either cluster-scoped resources (Nodes, PVs) or namespaced resources across all namespaces

* Unlike [[Role]], a ClusterRole is not namespaced as it exists at the cluster level
* Used directly via [[Cluster Role Bindings]] for cluster-wide access, or via [[RoleBindings]] to scope it to a single namespace

## ClusterRole Manifest

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: node-reader
rules:
  - apiGroups: [""]
    resources: ["nodes"]
    verbs: ["get", "list", "watch"]
  - apiGroups: ["storage.k8s.io"]
    resources: ["storageclasses", "persistentvolumes"]
    verbs: ["get", "list", "watch"]
```

## API Group Reference

| API Group | Resources |
|---|---|
| `""` (core) | pods, services, configmaps, secrets, namespaces, nodes, persistentvolumes |
| `apps` | deployments, replicasets, statefulsets, daemonsets |
| `batch` | jobs, cronjobs |
| `networking.k8s.io` | ingresses, networkpolicies |
| `rbac.authorization.k8s.io` | roles, clusterroles, rolebindings |
| `storage.k8s.io` | storageclasses, volumeattachments |
| `policy` | poddisruptionbudgets |

## Built-in ClusterRoles

| ClusterRole | Description |
|---|---|
| `cluster-admin` | Full access to everything |
| `admin` | Full namespaced access; can manage RBAC within a namespace |
| `edit` | Read/write most namespaced resources; cannot manage RBAC |
| `view` | Read-only access to most namespaced resources |
| `system:node` | Used by kubelets for node-level operations |

## Aggregated ClusterRoles

ClusterRoles can aggregate permissions from other ClusterRoles via label selectors

```YAML
aggregationRule:
  clusterRoleSelectors:
    - matchLabels:
        rbac.example.com/aggregate-to-monitoring: "true"
```

## Imperative Creation

```Bash
kubectl create clusterrole node-reader --verb=get,list,watch --resource=nodes
kubectl get clusterroles
kubectl describe clusterrole cluster-admin
```

## View a User's Effective Permissions

```Bash
# List all permissions for a user across the cluster
kubectl auth can-i --list --as=alice

# List permissions in a specific namespace
kubectl auth can-i --list --as=alice -n production
```
