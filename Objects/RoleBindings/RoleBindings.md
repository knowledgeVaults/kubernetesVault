A **[[RoleBindings]]** grants the permissions defined in a [[Role]] or [[Cluster Roles]] to a set of subjects within a single namespace

* Subjects can be: `User`, `Group`, or [[Service Accounts]]
* Can reference a `ClusterRole` because the permissions apply only within the RoleBinding's namespace (not cluster-wide)
* Changes to the referenced Role are automatically applied because there's no need to recreate the RoleBinding

## RoleBinding Manifest

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: default
subjects:
  - kind: User
    name: alice
    apiGroup: rbac.authorization.k8s.io
  - kind: Group
    name: developers
    apiGroup: rbac.authorization.k8s.io
  - kind: ServiceAccount
    name: my-app-sa
    namespace: default
roleRef:
  kind: Role            # or ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

## Subject Kinds

| Kind | Example Name | Notes |
|---|---|---|
| `User` | `alice` | External user authenticated via cert/OIDC |
| `Group` | `system:masters` | Group of users |
| `ServiceAccount` | `default` | In-cluster workload identity; requires `namespace` field |

## Binding a ClusterRole Locally

A RoleBinding can reference a ClusterRole (Uuseful for reusing common roles across namespaces)

```YAML
roleRef:
  kind: ClusterRole    # Permissions defined cluster-wide
  name: cluster-admin  # But scoped to this RoleBinding's namespace
  apiGroup: rbac.authorization.k8s.io
```

## Imperative Creation

```Bash
kubectl create rolebinding read-pods-binding \
  --role=pod-reader --user=alice -n default

kubectl create rolebinding sa-binding \
  --clusterrole=view --serviceaccount=default:my-app-sa -n staging
```

## Inspection

```Bash
kubectl get rolebindings -n NAMESPACE
kubectl describe rolebinding read-pods-binding -n default
kubectl auth can-i list pods --as=alice -n default
```

## RoleBinding Immutability

The `roleRef` field is immutable because you cannot change which Role a RoleBinding points to after creation. To change the referenced Role, delete and recreate the RoleBinding.
