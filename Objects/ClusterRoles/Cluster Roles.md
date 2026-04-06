
A `ClusterRole` is a RBAC object that defines a set of cluster-wide permissions

## ClusterRole Definition

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: OBJECT_NAME
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

* `rules` defines the list of permission rules where each rule specifies what actions a subject can perform on which resources
	* `apiGroups` specifies the core API group
	* `resources` specifies the Kubernetes objects that this rule applies to
	* `verbs` specifies the allowed actions and HTTP methods

| API Group                   | Resources                                                                      | apiVersion example            |
| --------------------------- | ------------------------------------------------------------------------------ | ----------------------------- |
| ""                          | *Pods*, *Services*, *ConfigMaps*, *Secrets*, *PersistentVolumes*, *Namespaces* | /api/v1                       |
| "apps"                      | *Deployments*, *ReplicaSets*, *StatefulSets*, *DaemonSets*                     | /apps/v1                      |
| "batch"                     | *Jobs*, *CronJobs*                                                             | /batch/v1                     |
| "networking.k8s.io"         | *Ingress*, *NetworkPolicy*                                                     | /networking.k8s.io/v1         |
| "rbac.authorization.k8s.io" | *Role*, *ClusterRole*, *RoleBinding*                                           | /rbac.authorization.k8s.io/v1 |
| "storage.k8s.io"            | *StorageClass*, *VolumeAttachment*                                             | /storage.k8s.io/v1            |
| "policy"                    | *PodDisruptionBudget*                                                          | /policy/v1                    |
