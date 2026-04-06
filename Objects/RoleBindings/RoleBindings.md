
A `RoleBinding` is a RBAC object that grants the permissions from a Role or ClusterRole to users, groups, or ServiceAccounts within a single namespace

## RoleBinding Definition

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding         
metadata:
  namespace: NAMESPACE
  name: OBJECT_NAME
subjects:
- kind: SUBJECT_KIND
  name: SUBJECT_NAME
  namespace: NAMESPACE
roleRef:
  kind: <Role|ClusterRole>              
  name: REFERENCE_NAME
  apiGroup: rbac.authorization.k8s.io
```