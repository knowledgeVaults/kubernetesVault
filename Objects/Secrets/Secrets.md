
**Secrets** are Kubernetes objects that store sensitive data as **base64-encoded** values

```YAML
apiVersion: v1
kind: Secret
metadata:
  name: OBJECT_NAME
  namespace: NAMESPACE
# Specify what type of secret this is
type: SECRET_TYPE
data:
  # Option 1: Already encoded
  KEY: BASE64_ENCODED_VALUE
  # Option 2: Plaintext (Kubernetes will encode them automatically)
  stringData:
    KEY: PLAINTEXT_VALUE
```

Common secret types

| Secret Type                      | Expected Value | Purpose |
| -------------------------------- | -------------- | ------- |
| `Opaque`                         |                |         |
| `kubernetes.io/tls`              |                |         |
| `kubernetes.io/dockerconfigjson` |                |         |
| `kubernetes.io/basic-auth`       |                |         |
| `kubernetes.io/ssh-auth`         |                |         |


* Not encrypted
* Enable encryption at rest for secrets so they're stored encrypted in ETCD
* Only sent to a node if a Pod on that node requires it
* The kubelet stores the secret into a tmpfs so that the secret is not written to disk storage
* The kubelet will delete its local copy of the secret once the Pod that depends on the secret is deleted

## Creating Secrets

### Imperative Approach

### Declarative Approach

```YAML
apiVersion:
kind:
metadata:
  name:
data:
  KEY: BASE64_ENCODED_VALUE
```

```Bash
echo -n "VALUE" | base64
```

## Injecting Secrets

```YAML
[...]
spec:
  containers:
    - name:
      image:
      ports:
        - containerPort:
      # Inject all ENV variables
      envFrom:
        - secretRef:
            name: SECRET_NAME
      # Inject a single ENV variable
      env:
        - name: ENV_VARIABLE_NAME
          valueFrom:
            secretKeyRef:
              name: SECRET_NAME
              key: ENV
      # Inject as a volume (Each secret is create as a file and its content contains the secret)
      volumes:
      - name: VOLUME_NAME
        secret:
          secretName: SECRET_NAME
```