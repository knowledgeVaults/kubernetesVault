
* Continuously monitors pod metrics via metrics server (Same as when running the `kubectl top` command)
* Automatically increases and decreases the number of pods based on resource consumption
* Relies either on internal sources (*metrics server or a custom metrics adapter*) or external adapters (*Datadog*, *Dynatrace*)

## Manual Scaling

```Bash
kubectl scale deployment DEPLOYMENT_NAME --replicas=REPLICA_COUNT
```

## Automatic Scaling

### Imperative Approach

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: DEPLOYMENT_NAME
spec:
  replicas: REPLICA_COUNT
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

Imperatively deploy a HPA and scales the number of pods if a pod starts consuming more that defined limit of resources
```Bash
kubectl autoscale deployment DEPLOYMENT_NAME --cpu-percent=DECIMAL_NUMBER --min=NUMBER --max=NUMBER
```

```Bash
kubectl delete hpa HPA_NAME
```

### Declarative Approach

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: HPA_NAME
spec:
  scaleTargetRef:
    apiVersion: app/v1
    kind: Deployment
    name: DEPLOYMENT_NAME
  minReplicas: NUMBER
  maxReplicas: NUMBER
  metrics:
    - type: Resource
      resource:
        name: {cpu|memory}
        target:
          type: Utilization
          averageUtilization: NUMBER
```