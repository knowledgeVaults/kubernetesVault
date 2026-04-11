The **[[etcd]] Server** is the process that runs the etcd key-value store, managing data persistence, [[Consensus]], and client connections

* The core binary is named `etcd` and runs as a daemon on each etcd member node
* Listens on two ports: `2379` (client API) and `2380` (peer-to-peer [[Control Plane/etcd/Leader Election]] communication)
* In [[kubeadm]] clusters, etcd runs as a Static Pod defined at `/etc/kubernetes/manifests/etcd.yaml`

## Key Startup Flags

```Bash
etcd \
  --name etcd-node1 \
  --data-dir /var/lib/etcd \
  --listen-client-urls https://127.0.0.1:2379 \
  --advertise-client-urls https://127.0.0.1:2379 \
  --listen-peer-urls https://127.0.0.1:2380 \
  --initial-advertise-peer-urls https://127.0.0.1:2380 \
  --initial-cluster "etcd-node1=https://127.0.0.1:2380" \
  --initial-cluster-state new \
  --cert-file=/etc/kubernetes/pki/etcd/server.crt \
  --key-file=/etc/kubernetes/pki/etcd/server.key \
  --client-cert-auth=true \
  --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt \
  --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt \
  --peer-key-file=/etc/kubernetes/pki/etcd/peer.key \
  --peer-client-cert-auth=true \
  --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

## Data Directory

etcd persists all data to `--data-dir` (default `/var/lib/etcd`)

* Contains the WAL (Write-Ahead Log) and snapshot files
* Must be backed up regularly using `etcdctl snapshot save`
* Should reside on a fast, dedicated disk (SSD recommended) to minimise Raft leader-heartbeat latency

## Health Checks

```Bash
# Check server health (requires running instance)
etcdctl endpoint health \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

# Check etcd Static Pod logs in kubeadm setup
kubectl logs -n kube-system etcd-NODENAME
```

## Performance Tuning

| Parameter | Recommendation |
|---|---|
| `--heartbeat-interval` | 100ms (default); lower on fast networks |
| `--election-timeout` | 1000ms (default); increase on high-latency networks |
| `--quota-backend-bytes` | 8GB max; trigger compaction/defrag when approaching limit |
| `--auto-compaction-mode` | `periodic` with `--auto-compaction-retention=1h` |
