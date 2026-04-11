`etcdctl` is the official CLI client for interacting with an [[etcd]] cluster, used for backup, restore, and cluster management tasks

* Ships as a standalone binary alongside etcd
* Must be used with `ETCDCTL_API=3` for the v3 API (default in modern etcd; v2 API is deprecated)
* Requires TLS certificates to connect to a secured etcd cluster (standard in Kubernetes deployments)

## Configuration

```Bash
export ETCDCTL_API=3
export ETCDCTL_ENDPOINTS=https://127.0.0.1:2379
export ETCDCTL_CACERT=/etc/kubernetes/pki/etcd/ca.crt
export ETCDCTL_CERT=/etc/kubernetes/pki/etcd/server.crt
export ETCDCTL_KEY=/etc/kubernetes/pki/etcd/server.key
```

## Snapshot Commands

```Bash
# Take a snapshot (backup)
etcdctl snapshot save /tmp/etcd-backup.db

# Verify snapshot integrity
etcdctl snapshot status /tmp/etcd-backup.db --write-out=table
```

## Key-Value Operations

```Bash
# Read a key
etcdctl get /registry/pods/default/my-pod

# List all keys (no values)
etcdctl get / --prefix --keys-only

# Write a key
etcdctl put /mykey "myvalue"

# Delete a key
etcdctl del /mykey

# Watch a key for changes
etcdctl watch /registry/pods/ --prefix
```

## Cluster Management

```Bash
# List all cluster members
etcdctl member list --write-out=table

# Check endpoint health
etcdctl endpoint health --write-out=table

# Check endpoint status (leader, DB size, Raft index)
etcdctl endpoint status --write-out=table

# Move leader to a different member
etcdctl move-leader MEMBER_ID
```

## Common Use Case: Pre-Upgrade Backup

```Bash
etcdctl snapshot save /opt/backup/etcd-$(date +%F).db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

## Lease Management

```Bash
# Create a lease (TTL in seconds)
etcdctl lease grant 300
# Returns: lease ID

# Attach a key to a lease (key is auto-deleted when lease expires)
etcdctl put /mykey "value" --lease=LEASE_ID

# Renew a lease
etcdctl lease keep-alive LEASE_ID

# Revoke a lease immediately
etcdctl lease revoke LEASE_ID
```

Leases are used by Kubernetes for [[Control Plane/etcd/Leader Election]] ([[kube-scheduler]], [[kube-controller-manager]]) and node heartbeats.
