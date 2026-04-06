
## Resource Configs

* The best way to backup resources is to query the kube-apiserver

```Bash
kubectl get all --all-namespaces -o yaml > FILENAME.yaml
```

## etcd

Use the built-in etcd snapshot solution
```Bash
# Take a snapshot
etcdctl snapshot save SNAPSHOT_NAME.db

# View the status of a snapshot
etcdctl snapshot status SNAPSHOT_NAME.db

# Restore an etcd snapshot
systemctl stop kube-apiserver
etcdctl snapshot restore SNAPSHOT_NAME.db --data-dir REPOSITORY_PATH # Then configure etcd to use the new data directory
systemctl daemon-reload
systemctl restart etcd
systemctl start kube-apiserver
```