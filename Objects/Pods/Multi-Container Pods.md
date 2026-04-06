

## Design Patterns

### Co-Located Containers

```YAML
apiVersion: v1
kind: Pod
metadata:
  name:
  labels:
    KEY: VALUE
spec:
  containers:
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
```

### Regular Init Containers

```YAML
apiVersion: v1
kind: Pod
metadata:
  name:
  labels:
    KEY: VALUE
spec:
  containers:
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
  initContainers:
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
    command: "SCRIPT_NAME.sh"
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
    command: "SCRIPT_NAME.sh"
```

### Sidecar Containers

```YAML
apiVersion: v1
kind: Pod
metadata:
  name:
  labels:
    KEY: VALUE
spec:
  containers:
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
  initContainers:
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
    command: "SCRIPT.sh"
    restartPolicy: RESTART_POLICY
```