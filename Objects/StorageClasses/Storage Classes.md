
* Enables dynamic provisioning of volumes
* Allows you to create different classes of service

## GCP

```YAML
apiVersion: storage.k8s.io/va
kind: StorageClass
metadata:
  name: STORAGE_CLASS_NAME
provisioner: PROVISIONER 
parameters:
  type: DISK_TYPE
  replication-type: REPLICATION_TYPE
```