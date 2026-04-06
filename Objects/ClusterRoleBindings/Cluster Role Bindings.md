
A `ClusterRoleBinding` is a RBAC object that ties a ClusterRole to one or more entities

* Applies the attached permissions from the ClusterRole cluster-wide

## ClusterRoleBinding Definition

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: OBJECT_NAME
subjects:
- kind: ServiceAccount
  name: my-app-sa
  namespace: default
roleRef:
  kind: ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

* `subjects` defines an array of identities that will inherit the permissions
	* `subjects.kind` specifies the type of subject that'll be inheriting the permissions (ex: *ServiceAccount*, *User*, *Group*)
	* `subjects.name` refers to the exact name of the entity

* `roleRef` refers to the source of the permissions being granted
	* `roleRef.kind` refers to the type of the referenced object (ex: *ClusterRole*)
	* `roleRef.name` refers to the exact name of the target reference
	* `roleRef.apiGroup` refers to the API group that owns the referenced object to ensure that there's no ambiguity across API groups