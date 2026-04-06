
Delete a single specific object
```Bash
kubectl delete OBJECT OBJECT_NAME
```

Delete all specific objects from a namespace
```Bash
kubectl delete OBJECT --all -n NAMESPACE
```

Delete all resources in a namespace
```Bash
kubectl delete all --all -n NAMESPACE
```

Delete a ReplicaSet
```Bash
kubectl delete replicaset REPLICA_SET -n NAMESPACE
```

Replace an object
```Bash
kubectl replace [--force] -f FILENAME.yaml
```

tmp
```Bash
kubectl proxy
```
* An HTTP proxy that's used to access the kube-apiserver