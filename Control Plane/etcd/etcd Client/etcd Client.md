The **[[etcd]] client** is the library or CLI tool used to communicate with the etcd server via gRPC

* Kubernetes components ([[kube-apiserver]], etc.) use the official Go etcd client library (`go.etcd.io/etcd/client/v3`) internally
* Administrators use `etcdctl` or `etcdutl` for direct cluster management and inspection
* All communication with etcd is encrypted via mutual TLS in production clusters

## etcd Client v3 Protocol

etcd v3 uses a gRPC-based protocol (replacing the HTTP/JSON v2 API)

* Operates over a persistent HTTP/2 connection for efficiency
* Supports watch streams, leases, transactions, and range queries
* Authentication is done via TLS certificates or username/password tokens

## Connection Configuration

When connecting to etcd in a Kubernetes cluster, TLS certificates are always required

```Bash
etcdctl   --endpoints=https://127.0.0.1:2379   --cacert=/etc/kubernetes/pki/etcd/ca.crt   --cert=/etc/kubernetes/pki/etcd/server.crt   --key=/etc/kubernetes/pki/etcd/server.key   COMMAND
```

## Environment Variables (Alternative to Flags)

```Bash
export ETCDCTL_API=3
export ETCDCTL_ENDPOINTS=https://127.0.0.1:2379
export ETCDCTL_CACERT=/etc/kubernetes/pki/etcd/ca.crt
export ETCDCTL_CERT=/etc/kubernetes/pki/etcd/server.crt
export ETCDCTL_KEY=/etc/kubernetes/pki/etcd/server.key
```

## Common Client Operations

```Bash
etcdctl get /registry/pods/default/my-pod        # Read a key
etcdctl get /registry/ --prefix --keys-only      # List all keys
etcdctl put /mykey "myvalue"                     # Write a key
etcdctl del /mykey                               # Delete a key
etcdctl watch /registry/pods/ --prefix           # Watch for changes
etcdctl member list                              # List cluster members
etcdctl endpoint health                          # Check endpoint health
etcdctl snapshot save /tmp/backup.db             # Take a snapshot
```

## Restoring from Snapshot

```Bash
etcdutl snapshot restore /tmp/backup.db   --data-dir /var/lib/etcd-restored   --name etcd-node1   --initial-cluster etcd-node1=https://127.0.0.1:2380   --initial-cluster-token etcd-cluster-1   --initial-advertise-peer-urls https://127.0.0.1:2380
```
