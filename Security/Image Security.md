
**[[Image Security]]** in Kubernetes involves ensuring that container images are sourced from trusted registries, are free of critical vulnerabilities, and are pulled with appropriate credentials

## Image Name Format

```
REGISTRY/ACCOUNT/REPOSITORY:TAG
```

* Default registry: `docker.io` (e.g., `nginx` → `docker.io/library/nginx:latest`)
* `library` is the default Docker Hub account for official images
* Always pin to a specific tag or digest in production and avoid `:latest`

## Private Registry Authentication

Create a `docker-registry` [[Secrets]] with credentials and reference it in the pod spec

```Bash
kubectl create secret docker-registry my-registry-secret \
  --docker-server=registry.example.com \
  --docker-username=my-user \
  --docker-password=my-password \
  --docker-email=user@example.com
```

```YAML
spec:
  imagePullSecrets:
    - name: my-registry-secret
  containers:
    - name: app
      image: registry.example.com/my-org/my-app:v1.2.3
```

## Image Pull Policy

| Policy         | Behaviour                                                                     |
| -------------- | ----------------------------------------------------------------------------- |
| `IfNotPresent` | Pull only if not cached on the node (default for tags other than `latest`)    |
| `Always`       | Pull on every pod start which ensures latest image (default for `latest` tag) |
| `Never`        | Never pull and the image must exist on the node                               |

```YAML
spec:
  containers:
    - name: app
      image: my-app:v2
      imagePullPolicy: IfNotPresent
```

## Image Vulnerability Scanning

Scan images in CI/CD pipelines before [[Deployments]]

```Bash
# Trivy (open-source scanner)
trivy image my-app:v2
trivy image --severity CRITICAL,HIGH my-app:v2
```

## Enforcing Image Policy via Admission

Use the [[ImagePolicyWebhook]] [[Admission Controllers]] to block images from untrusted registries at the [[kube-apiserver]] level

```Bash
# kube-apiserver flag
--enable-admission-plugins=ImagePolicyWebhook
--admission-control-config-file=/etc/kubernetes/admission-config.yaml
```

## Image Digest Pinning

Use SHA256 digests instead of mutable tags to prevent tag mutation attacks

```YAML
image: nginx@sha256:abc123...   # Immutable; always the exact same image
```
