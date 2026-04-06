
## Namespaced RBAC

* Managed via [[Roles]] and linked to a user via [[RoleBindings]]

Check what access you have in a cluster
```Bash
kubectl auth can-i COMMAND [--namespace NAMESPACE]
```

Test permissions by impersonating another user
```Bash
kubectl auth can-i COMMAND --as USER [--namespace NAMESPACE]
```

## Cluster Scoped RBAC

* Managed via [[Cluster Roles]] and linked to users via [[Cluster Role Bindings]]