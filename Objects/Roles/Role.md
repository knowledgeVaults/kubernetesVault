A **Role** is an [[RBAC]] object that defines a set of permissions within a single namespace

* Contains one or more `rules` that specify which verbs are allowed on which resources
* Only grants access within the namespace where it is created
* Must be bound to subjects via a [[RoleBindings]] to take effect

## Role Manifest

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
  - apiGroups: [""]            # "" = core API group
    resources: ["pods", "pods/log"]
    verbs: ["get", "list", "watch"]
  - apiGroups: ["apps"]
    resources: ["deployments"]
    verbs: ["get", "list", "watch", "create", "update", "patch"]
```

## Verbs Reference

| Verb | HTTP Method | Description |
|---|---|---|
| `get` | GET | Read a single named resource |
| `list` | GET | List all resources of a type |
| `watch` | GET (streaming) | Watch for change events |
| `create` | POST | Create a new resource |
| `update` | PUT | Replace an existing resource |
| `patch` | PATCH | Partially modify a resource |
| `delete` | DELETE | Delete a single resource |
| `deletecollection` | DELETE | Delete all matching resources |

## Resource Names (Fine-Grained)

Restrict a rule to specific named resources

```YAML
rules:
  - apiGroups: [""]
    resources: ["configmaps"]
    resourceNames: ["app-config", "feature-flags"]
    verbs: ["get"]
```

## Subresources

Grant access to subresources like `pods/exec`, `pods/log`, `deployments/scale`

```YAML
rules:
  - apiGroups: [""]
    resources: ["pods/exec"]
    verbs: ["create"]
```

## Imperative Creation

```Bash
kubectl create role pod-reader --verb=get,list,watch --resource=pods -n default
kubectl create role deploy-editor --verb=get,list,create,update,patch --resource=deployments -n staging
```

## Inspection

```Bash
kubectl get roles -n NAMESPACE
kubectl describe role pod-reader -n default
kubectl auth can-i get pods --as=alice -n default
```

## Role vs [[Cluster Roles]]

Use `Role` when permissions should be scoped to a single namespace. Use `ClusterRole` when you need cluster-wide access or want to share the same role definition across multiple namespaces via separate RoleBindings.
