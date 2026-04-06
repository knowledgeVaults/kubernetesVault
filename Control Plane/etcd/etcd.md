
`etcd` is a distributed and strongly consistent key-value store that manages critical data in distributed systems

* The primary datastore for Kubernetes clusters
* Stores the cluster's state, configuration data, and metadata 
* Uses the **Raft consensus** algorithm to ensure high availability and fault tolerance across multiple nodes
* Handles leader elections during network partitions
* Replicates data fully to prevent single points of failures
* The API server relies on `etcd` to persist all object states

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