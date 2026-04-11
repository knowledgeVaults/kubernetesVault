`etcdutl` is the offline utility tool for [[etcd]] that operates directly on etcd data files without requiring a running etcd server

* Introduced to replace the restore and defragment subcommands previously handled by `etcdctl` when offline access is needed
* Does **not** connect to a live etcd server since it reads and writes to local database files directly
* Used primarily for snapshot restoration and database compaction/defragmentation

## Key Difference vs etcdctl

| Tool | Requires Running etcd | Primary Use |
|---|---|---|
| `etcdctl` | Yes | Backup, cluster management, live key-value ops |
| `etcdutl` | No | Restore, defragment, hash verification on local files |

## Snapshot Restore

Restoring a snapshot creates a new etcd data directory from a backup

```Bash
etcdutl snapshot restore /opt/backup/etcd-backup.db \
  --data-dir /var/lib/etcd-restored \
  --name etcd-node1 \
  --initial-cluster "etcd-node1=https://127.0.0.1:2380" \
  --initial-cluster-token etcd-cluster-1 \
  --initial-advertise-peer-urls https://127.0.0.1:2380
```

After restore, update the etcd static pod manifest to point to the new data directory:
```Bash
# Edit /etc/kubernetes/manifests/etcd.yaml
# Change: --data-dir=/var/lib/etcd
# To:     --data-dir=/var/lib/etcd-restored
# Also update the hostPath volume mount accordingly
```

## Snapshot Verification

```Bash
etcdutl snapshot status /opt/backup/etcd-backup.db --write-out=table
# Shows: Hash, Revision, Total Keys, Total Size
```

## Defragmentation

Defragmenting reclaims space from deleted keys in the data file

```Bash
etcdutl defrag --data-dir /var/lib/etcd
```

* Run after heavy delete operations to reclaim disk space
* The etcd service must be stopped before running offline defrag
* For online defrag, use `etcdctl defrag --endpoints=...` instead

## Hash Comparison

```Bash
etcdutl snapshot status /opt/backup/etcd-backup.db
# Outputs: DB Hash, Revision, Total Keys, Total Size
```

Compare the hash before and after restore to confirm data integrity. Always verify the snapshot before relying on it for a production restore.
