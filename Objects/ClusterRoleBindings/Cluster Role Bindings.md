A **[[Cluster Role Bindings]]** ties a [[Cluster Roles]] to subjects cluster-wide — granting permissions across all namespaces and cluster-scoped resources

* Unlike [[RoleBindings]], a ClusterRoleBinding cannot be scoped to a single namespace
* Subjects can be: `User`, `Group`, or [[Service Accounts]]
* Once created, the `roleRef` field is immutable — cannot be changed without deleting and recreating the binding

## ClusterRoleBinding Manifest

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: node-reader-binding
subjects:
  - kind: User
    name: alice
    apiGroup: rbac.authorization.k8s.io
  - kind: Group
    name: ops-team
    apiGroup: rbac.authorization.k8s.io
  - kind: ServiceAccount
    name: monitoring-agent
    namespace: monitoring
roleRef:
  kind: ClusterRole
  name: node-reader
  apiGroup: rbac.authorization.k8s.io
```

## Subject Kinds

| Kind | Example Name | Notes |
|---|---|---|
| `User` | `alice` | Authenticated via cert CN or OIDC claim |
| `Group` | `system:masters` | All users in the group get the permissions |
| `ServiceAccount` | `monitoring-agent` | Must specify `namespace` where the SA lives |

## Common Cluster-Admin Binding Pattern

```YAML
subjects:
  - kind: User
    name: platform-admin
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

Granting `cluster-admin` gives unrestricted access — use sparingly and audit regularly.

## Imperative Creation

```Bash
kubectl create clusterrolebinding node-reader-binding \
  --clusterrole=node-reader --user=alice

kubectl create clusterrolebinding sa-cluster-admin \
  --clusterrole=cluster-admin --serviceaccount=kube-system:my-sa
```

## Inspection and Audit

```Bash
kubectl get clusterrolebindings
kubectl describe clusterrolebinding node-reader-binding
kubectl auth can-i list nodes --as=alice
kubectl auth can-i '*' '*' --as=alice    # Check if user has all permissions
```

## Common System ClusterRoleBindings

```Bash
kubectl get clusterrolebindings | grep system:
# system:node, system:kube-controller-manager, system:kube-scheduler, etc.
```

Do not modify system ClusterRoleBindings as they are required for Kubernetes components to function.
