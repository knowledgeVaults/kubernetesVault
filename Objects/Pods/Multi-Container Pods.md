

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

## Container Communication

All containers in a Pod share the same [[Network Namespaces]] since they communicate on `localhost`

```YAML
spec:
  containers:
    - name: app
      image: my-app:v1
      ports:
        - containerPort: 8080
    - name: sidecar-proxy
      image: envoy-proxy:v1
      ports:
        - containerPort: 9090
      # Communicates with app at localhost:8080
```

## Shared Volumes

Containers share volumes by mounting the same `emptyDir` or PVC

```YAML
volumes:
  - name: shared-data
    emptyDir: {}
containers:
  - name: producer
    volumeMounts:
      - name: shared-data
        mountPath: /output
  - name: consumer
    volumeMounts:
      - name: shared-data
        mountPath: /input
```

## Design Patterns

| Pattern | Use Case |
|---|---|
| **Sidecar** | Extend main app without modifying it (logging, TLS, metrics) |
| **Init Container** | Pre-start setup that must complete before the app starts |
| **Ambassador** | Proxy external communication (e.g., Envoy, Istio sidecar) |
| **Adapter** | Standardise output format (e.g., convert app metrics to Prometheus format) |

## Resource Accounting

Each container specifies its own resource requests and limits independently. The pod's total resource footprint is the sum of all container requests.

```Bash
kubectl top pod MY_POD --containers    # Per-container CPU/memory usage
```
