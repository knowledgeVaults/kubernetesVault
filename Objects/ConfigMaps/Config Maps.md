**ConfigMaps** store non-confidential configuration data as key-value pairs or file contents, decoupling application configuration from container images

* Not encrypted so do not store passwords or tokens in ConfigMaps (Use Secrets instead)
* Keys must be alphanumeric characters, `-`, `_`, or `.`
* Injected into pods as environment variables, command-line arguments, or as files via a volume mount

## [[Config Maps]] Manifest

```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: default
data:
  APP_ENV: "production"
  LOG_LEVEL: "info"
  config.json: |
    {
      "server": "api.example.com",
      "timeout": 30
    }
binaryData:
  ca-cert.pem: LS0tLS1CRUdJTi...   # Base64-encoded binary data
```

## Injecting ConfigMaps into Pods

### Environment Variables

```YAML
envFrom:
  - configMapRef:
      name: app-config     # Inject all keys as env vars

env:
  - name: LOG_LEVEL
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: LOG_LEVEL     # Inject a single key
```

### Volume Mount (as files)

```YAML
volumes:
  - name: config-volume
    configMap:
      name: app-config

containers:
  - volumeMounts:
      - name: config-volume
        mountPath: /etc/config    # Each key becomes a file in this directory
```

## Imperative Commands

```Bash
# From literals
kubectl create configmap app-config --from-literal=APP_ENV=production --from-literal=LOG_LEVEL=info

# From a file
kubectl create configmap nginx-config --from-file=nginx.conf

# From a directory (each file becomes a key)
kubectl create configmap app-config --from-file=./config/

# Edit live ConfigMap
kubectl edit configmap app-config
```

## Hot Reloading

Pods using ConfigMaps via volume mounts see updates automatically (within ~1 minute). Pods using `envFrom`/`env` require a restart to pick up changes.

## Immutable ConfigMaps

Mark a ConfigMap as immutable to prevent accidental changes and improve performance ([[kubelet]] doesn't poll for updates)

```YAML
immutable: true
```

Once set to `immutable: true`, the field cannot be changed — delete and recreate the ConfigMap.

## Watching for ConfigMap Changes

ConfigMaps mounted as volumes are updated automatically within ~1 minute when the ConfigMap changes. Environment variable injection requires a pod restart.

```Bash
# Force pod restart to pick up new env var values
kubectl rollout restart deployment/MY_DEPLOYMENT
```

## Commands

```Bash
kubectl get configmap -n NAMESPACE
