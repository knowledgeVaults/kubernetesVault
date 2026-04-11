
**Sidecar Containers** are auxiliary containers that run alongside the main application container in the same Pod, extending or enhancing its functionality without modifying the main container image

* Introduced as a first-class feature in Kubernetes 1.29 via `initContainers` with `restartPolicy: Always`
* Previously, sidecars were regular containers in the `containers` list; the new model gives them init-container-like startup ordering guarantees
* Share the same [[Network Namespaces]], volumes, and pod lifecycle as the main container

## Classic Sidecar Pattern (Pre-1.29)

Defined as a regular container — starts in parallel with the main container; no ordering guarantee

```YAML
spec:
  containers:
    - name: app
      image: my-app:v1
    - name: log-shipper
      image: fluent/fluent-bit:latest
      volumeMounts:
        - name: log-volume
          mountPath: /var/log
```

## Native Sidecar (Kubernetes 1.29+)

Defined in `initContainers` with `restartPolicy: Always` that always starts before main containers and stays running

```YAML
spec:
  initContainers:
    - name: log-shipper
      image: fluent/fluent-bit:latest
      restartPolicy: Always
      volumeMounts:
        - name: log-volume
          mountPath: /var/log
  containers:
    - name: app
      image: my-app:v1
      volumeMounts:
        - name: log-volume
          mountPath: /var/log/app
```

## Advantages of Native Sidecars

* Guaranteed to start before main containers (init ordering)
* Guaranteed to stop after main containers (graceful termination order)
* Keeps running if it crashes because `restartPolicy: Always` triggers restart independently of the main container

## Common Sidecar Use Cases

| Use Case | Example Sidecar |
|---|---|
| Log shipping | [[Fluent Bit]], Fluentd |
| Service mesh proxy | Istio Envoy, Linkerd proxy |
| [[Secrets]] rotation | Vault agent injector |
| Metrics collection | Prometheus exporter |
| Config sync | Git sync sidecar |

## Sidecar vs Init Container Comparison

| Feature | Sidecar (native, 1.29+) | Init Container |
|---|---|---|
| Runs during pod lifetime | Yes (restartPolicy: Always) | No (exits before app starts) |
| Start order | Before main containers | Before all containers |
| Stop order | After main containers | N/A |
| Use case | Ongoing support tasks | One-time setup |

## Common Sidecar Examples

```YAML
# Git-sync sidecar: continuously syncs a git repo into a volume
initContainers:
  - name: git-sync
