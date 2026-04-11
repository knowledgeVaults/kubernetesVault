
Infrastructure-as-Code (**IaC**) is the practice of managing and provisioning infrastructure using code and automation instead of manual configuration

## Infrastructure-as-Code Approaches

### Imperative

The imperative approach gives the system step-by-steps instructions on how to create and modify resources

* The user manually controls every action

Example: *Deploying NGINX*
```Bash
# Create a deployment
kubectl create deployment nginx --image=nginx

# Scale the deployment
kubectl scale deployment nginx --replicas=3

# Expose the deployment
kubectl expose deployment nginx --port=80
```

### Declarative

The declarative approach describes the desired final state 

* The user defines the infrastructure in a configuration file (YAML) and lets the system figure out how to reach that state
* Kubernetes will constantly compare the resource's desired state with its actual state and make updates if needed to ensure that it's actual state matches its desired state

Example: *Deploying NGINX*
```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

```Bash
kubectl apply -f deployment.yaml
```

## Declarative Everything

Store all Kubernetes manifests in Git — never make undocumented manual `kubectl apply` or `kubectl edit` changes in production

* Use GitOps tools ([[Argo CD]], Flux) to enforce Git as the single source of truth
* All changes go through code review before reaching the cluster
* Rollback becomes a `git revert` followed by an automated sync

## Tooling Options

| Tool | Description |
|---|---|
| **[[Helm]]** | Package manager with templating and release management |
| **[[Kustomize]]** | Overlay-based config management; built into kubectl |
| **Terraform** | Provisions the cluster infrastructure and optionally Kubernetes resources |
| **Pulumi** | Infrastructure-as-code using general-purpose programming languages |

## Directory Structure Best Practice

```
infrastructure/
├── base/              # Common manifests used in all environments
│   ├── deployment.yaml
│   └── service.yaml
└── overlays/
    ├── staging/       # Staging-specific patches (image tag, replicas)
    └── production/    # Production-specific patches
```

## CI/CD Pipeline

```
Git push → CI (lint YAML, validate schemas, security scan)
                │
         Merge to main → Argo CD sync → cluster
```

## Schema Validation

