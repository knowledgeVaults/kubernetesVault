**Cluster Backups** protect against data loss by capturing the state of both Kubernetes resource configurations and [[etcd]] data

## Strategy 1: Resource Configuration Backup

Export all cluster resources to YAML files via the Kubernetes API

```Bash
# Backup all namespaced resources
kubectl get all --all-namespaces -o yaml > cluster-resources.yaml

# More comprehensive: export specific resource types
for resource in deployments services configmaps secrets ingresses pvc; do
  kubectl get $resource --all-namespaces -o yaml > backup-$resource.yaml
done
```

**Limitation**: `kubectl get all` does not include every resource type. Use tools like Velero for complete backups.

## Strategy 2: etcd Snapshot

etcd holds all cluster state (A snapshot is the most complete backup)

```Bash
# Take a snapshot
ETCDCTL_API=3 etcdctl snapshot save /opt/backup/etcd-$(date +%F).db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

# Verify snapshot integrity
etcdctl snapshot status /opt/backup/etcd-$(date +%F).db --write-out=table
```

### Restoring from etcd Snapshot

```Bash
systemctl stop kube-apiserver
etcdutl snapshot restore /opt/backup/etcd-backup.db \
  --data-dir /var/lib/etcd-restored
# Update etcd static pod manifest --data-dir to point to restored directory
systemctl daemon-reload
systemctl restart etcd
systemctl start kube-apiserver
```

## Strategy 3: Velero (Recommended for Production)

Velero automates Kubernetes backup and restore including PersistentVolumes

```Bash
# Install
velero install --provider aws --plugins velero/velero-plugin-for-aws \
  --bucket my-backup-bucket --backup-location-config region=us-east-1

# Create a backup
velero backup create cluster-backup --include-namespaces "*"

# Restore
velero restore create --from-backup cluster-backup
```

### Backup Schedule Using Velero

```Bash
velero schedule create daily-backup --schedule="0 2 * * *"
```
