
```YAML
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: DAEMONSET_NAME
  namespace: NAMESPACE
spec:
  selector:
    matchLabels:
      KEY: VALUE
  template:
    metadata:
      labels:
        KEY: VALUE
    spec:
      containers:
      - name: CONTAINER_NAME
        image: IMAGE[:TAG]
```
