A **[[Stateful Sets]]** manages stateful applications by providing ordered [[Deployments]], stable network identities, and stable persistent storage for each pod replica

* Each pod gets a predictable, stable hostname: `POD_NAME-0`, `POD_NAME-1`, etc.
* Pods are created sequentially (0, 1, 2…) and deleted in reverse order
* Each pod can have its own [[PersistentVolumeClaims]] that persists independently of the pod lifecycle

## When to Use StatefulSets

* Databases (MySQL, PostgreSQL, MongoDB, Cassandra)
* Message queues that require persistent state (Kafka)
* Any application that requires stable network identity or storage ordering

## StatefulSet Manifest

```YAML
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-db
spec:
  serviceName: "my-db"       # Headless service name for stable DNS
  replicas: 3
  selector:
    matchLabels:
      app: my-db
  template:
    metadata:
      labels:
        app: my-db
    spec:
      containers:
        - name: db
          image: postgres:15
          volumeMounts:
            - name: data
              mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 10Gi
        storageClassName: fast-ssd
```

## Stable DNS Names

Each pod in a StatefulSet gets a DNS name via the headless Service

```
POD_NAME-INDEX.SERVICE_NAME.NAMESPACE.svc.cluster.local
my-db-0.my-db.default.svc.cluster.local
my-db-1.my-db.default.svc.cluster.local
```

## Update Strategy

```YAML
updateStrategy:
  type: RollingUpdate          # Default: updates pods one at a time, highest ordinal first
  # type: OnDelete             # Manual: only updates when pod is deleted
```

## Commands

```Bash
kubectl get statefulset
kubectl scale statefulset my-db --replicas=5
kubectl rollout status statefulset/my-db
kubectl delete statefulset my-db          # Pods deleted in reverse order
kubectl delete statefulset my-db --cascade=orphan  # Delete SS only; pods remain
```
