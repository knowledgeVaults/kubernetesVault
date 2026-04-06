
A `Deployment` is a resource that manages and automates the deployment of applications using `ReplicaSets` and `Pods`

* Ensures that a specified number of application instances are always running and updated safely
* Requires `labels` and `selectors` to allow the Deployment to identify, create, and manage the correct Pods
* Deleted and creates a new Pod whenever changes are made to the Deployment

## Deployment Declaration

```YAML
spec:
  replicas: NUMBER_OF_REPLICAS
  
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
          image: IMAGE
```

```Bash
kubectl edit deployment DEPLOYMENT_NAME
```