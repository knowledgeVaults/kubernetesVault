**[[RBAC]] (Role-Based Access Control)** is the standard Kubernetes authorization mechanism that grants permissions based on roles assigned to users, groups, or service accounts

* Managed via four API objects: [[Role]], [[Cluster Roles]], [[RoleBindings]], [[Cluster Role Bindings]]
* Enabled via `--authorization-mode=RBAC` on the [[kube-apiserver]] (default in all modern clusters)
* Follows an explicit allow model where everything is denied by default unless a rule grants it)

## Namespaced vs Cluster-Scoped

| Object | Scope | Effect |
|---|---|---|
| `Role` | Namespace | Grants permissions within one namespace |
| `RoleBinding` | Namespace | Binds a Role (or ClusterRole) to subjects within one namespace |
| `ClusterRole` | Cluster | Grants permissions cluster-wide or for non-namespaced resources |
| `ClusterRoleBinding` | Cluster | Binds a ClusterRole to subjects cluster-wide |

## Checking Permissions

```Bash
# Check if current user can perform an action
kubectl auth can-i get pods -n default

# Impersonate another user
kubectl auth can-i get secrets --as=alice -n production

# List all permissions in a namespace for a user
kubectl auth can-i --list --as=alice -n default
```

## Common Role Examples

```YAML
# Read-only access to pods in a namespace
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
```

## Verbs

| Verb | HTTP Method | Description |
|---|---|---|
| `get` | GET | Read a single object |
| `list` | GET | List all objects of a kind |
| `watch` | GET + watch stream | Receive change events |
| `create` | POST | Create a new object |
| `update` | PUT | Replace an existing object |
| `patch` | PATCH | Partially update an object |
| `delete` | DELETE | Remove an object |
| `deletecollection` | DELETE | Remove all matching objects |

## Aggregated ClusterRoles

ClusterRoles can be composed from smaller roles via aggregation labels

```YAML
aggregationRule:
  clusterRoleSelectors:
    - matchLabels:
        rbac.example.com/aggregate-to-monitoring: "true"
```

Any ClusterRole with the matching label is automatically merged into this aggregated role.
