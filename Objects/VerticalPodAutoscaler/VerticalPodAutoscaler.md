
* Doesn't come installed by default (You'll need to deploy it)
* Continuously monitors pod metrics via metrics server (Same as when running the `kubectl top` command)
* Automatically increases and decreases the number of pods based on resource consumption
* Relies either on internal sources (*metrics server or a custom metrics adapter*) or external adapters (*Datadog*, *Dynatrace*)

## Manual Vertical Scaling

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: DEPLOYMENT_NAME
spec:
  replicas: NUMBER
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
          resources:
            requests:
              cpu: "VALUE"
              memory: "VALUE"
            limits:
              cpu: "VALUE"
              memory: "VALUE"
```

```Bash
kubectl top pod POD_NAME -n NAMESPACE
```

## Declarative 

* There's no imperative way to vertically scale pods since it's not a built-in component

```YAML
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: VPA_NAME
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: DEPLOYMENT_NAME
  updatePolicy:
    updateMode: "{Auto|Off|Initial|Recreate}"
  resourcePolicy:
    containerPolicies:
      - containerName: "CONTAINER_NAME"
        minAllowed:
          cpu: "VALUE"
          memory: "VALUE"
        controlledResources: ["cpu","memory"]
```

| VPA Update Mode | Description                                                                     |
| --------------- | ------------------------------------------------------------------------------- |
| `Off`           | Only recommends and doesn't change anything                                     |
| `Initial`       | Only changes on Pod creater                                                     |
| `Recreate`      | Evicts the current pods if their usage goes beyond it's current specified range |
| `Auto`          | Updates existing pods to the recommended numbers                                |