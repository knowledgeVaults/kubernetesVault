
**Init Containers** are specialised containers in a Pod that run and complete before the main application containers start

* Defined separately from the main `containers` list under `spec.initContainers`
* Run sequentially in the order defined where each must complete successfully before the next starts
* If an init container fails, Kubernetes restarts the pod according to the pod's `restartPolicy`
* The main containers do not start until all init containers have completed successfully

## Use Cases

* **Waiting for dependencies**: wait for a database or external service to become available before the app starts
* **Pre-population of volumes**: download configuration files, clone Git repos, or run database migrations
* **Permission setup**: create files or set filesystem permissions that the main container needs
* **Security separation**: run privileged setup tasks that the main container should not have access to

## Init Container Manifest

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  initContainers:
    - name: wait-for-db
      image: busybox
      command: ['sh', '-c', 'until nslookup db-service; do sleep 2; done']
    - name: migration
      image: my-app-migrator:v1
      command: ['python', 'manage.py', 'migrate']
      envFrom:
        - secretRef:
            name: db-credentials
  containers:
    - name: my-app
      image: my-app:v1
      ports:
        - containerPort: 8080
```

## Key Differences from Regular Containers

| Feature | Init Container | Regular Container |
|---|---|---|
| Runs | Before app containers | Continuously |
| Execution | Sequential, one at a time | Parallel |
| Completion | Must exit with code 0 | Runs indefinitely |
| Resource accounting | Counted separately | Counted together |
| Probes | No readiness/liveness probes | Supports all probe types |

## Shared Volumes

Init containers frequently use shared volumes to pass data to main containers

```YAML
volumes:
  - name: shared-data
    emptyDir: {}
initContainers:
  - name: download-config
    image: busybox
    command: ['wget', '-O', '/data/config.json', 'https://config.example.com/config']
    volumeMounts:
      - name: shared-data
        mountPath: /data
containers:
  - name: app
    volumeMounts:
      - name: shared-data
        mountPath: /app/config
```
