
## Taints

Taint a node
```Bash
kubectl taint node NODE_NAME KEY=VALUE:TAINT_EFFECT
```

| Taint effect       | Description |
| ------------------ | ----------- |
| `NoSchedule`       |             |
| `PreferNoSchedule` |             |
| `NoExecute`        |             |
Untaint a node
```Bash
kubectl taint node NODE_NAME KEY=VALUE-
```

## Tolerations

Apply toleration to a Pod
```Bash
apiVersion: v1
kind: Pod
metadata: 
  name: POD_NAME
spec:
  containers:
    - name: CONTAINER_NAME
      image: IMAGE
  tolerations:
    - key: KEY
      operator: "OPERATOR"
      value: VALUE
      effect: TAINT_EFFECT
```