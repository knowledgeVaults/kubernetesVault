
[[etcd]] is a distributed and strongly consistent key-value store that manages critical data in distributed systems

* The primary datastore for Kubernetes clusters
* Stores the cluster's state, configuration data, and metadata 
* Uses the **[[Control Plane/etcd/Leader Election]] [[Consensus]]** algorithm to ensure high availability and fault tolerance across multiple nodes
* Handles leader elections during network partitions
* Replicates data fully to prevent single points of failures
* The [[kube-apiserver]] relies on `etcd` to persist all object states

* Ensures that the same data is available on all etcd instances at the same time
* Follows the election process to choose one leader node 
* The leader node makes sure that the other nodes receive a copy of its data

## etcd Client

./etcdctl

```
[...]
- --key-file=
- --cert-file=
- --peer-cert-file=
- --peer-client-cert-auth={true|false}
- --peer-key-file=
- --peer-trusted-ca-file=
- --trusted-ca-file=
[...]
```

## tmp

View the etcd version by checking the image version
```Bash
kubectl describe pod ETCD_POD_NAME -n kube-system
```

## etcd in Kubernetes

The kube-apiserver connects to etcd to read and write all cluster state

* Every Kubernetes object (Pods, Deployments, ConfigMaps, Secrets, etc.) is stored in etcd
* Only the kube-apiserver communicates with etcd directly because all other components go through the API server
* etcd storage path structure: `/registry/RESOURCE_TYPE/NAMESPACE/NAME`

## Inspecting etcd Data

```Bash
# View a pod's raw etcd entry
etcdctl get /registry/pods/default/my-pod \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

# List all keys
etcdctl get / --prefix --keys-only \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

## etcd Health Monitoring

```Bash
etcdctl endpoint health --write-out=table
etcdctl endpoint status --write-out=table
# Columns: endpoint, ID, version, DB SIZE, IS LEADER, IS LEARNER, RAFT TERM, RAFT INDEX
```
