
A `Role` is a RBAC object that defines permissions within a single namespace

## Role Definition

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: Role           
metadata:
  namespace: NAMESPACE
  name: OBJECT_NAME
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]

```