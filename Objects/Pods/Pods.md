
## Pod Definition

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: 
spec:
  containers:
  - name:
    image:
    ports:
    - containerPort:
```

### API Version

### Kind

### Metadata

### Specification

* Can't be edited when the Pod is running except for the following via `kubectl edit pod POD_NAME -n NAMESPACE`: `spec.containers[*].image`, `spec.initContainers[*].image`, `spec.activeDeadllineSeconds`, `spec.tolerations`
* Editing the `spec:` field while the Pod is running will save it in a temporary file (Delete the running Pod and create it using the temporary file)

```YAML
spec:
  containers:
    - name: CONTAINER_NAME
      image: IMAGE[:TAG]
      command: ["COMMAND"]
      args: ["ARGUMENT"]
      env:
        # Static definition
        - name: ENVIRONMENTAL_VARIABLE_NAME
          value: VALUE
        # Specify a single environmental variable from a ConfigMap
        - name: ENVIRONMENTAL_VARIABLE_NAME
          valueFrom:
            configMapKeyRef:
        # via Secret
        - name: ENVIRONMENTAL_VARIABLE_NAME
          valueFrom:
            secretKeyRef:
      # Inject all environmental variables specified in ConfigMap
      envFrom:
      - configMapRef:
          name: CONFIG_MAP_NAME
```
## tmp

Create a Pod
```Bash
kubectl run POD_NAME --image=IMAGE --namespace=NAMESPACE
```

Generate a Pod manifest file without creating it
```Bash
kubetl run --image=IMAGE --dry-run={client|server} -o yaml
```

View pod resource consumption (Must have metrics server installed)
```Bash
kubectl top pod [POD_NAME] -n NAMESPACE
```