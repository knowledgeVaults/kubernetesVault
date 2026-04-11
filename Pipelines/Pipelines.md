
**CI/CD Pipelines** in Kubernetes automate the process of building, testing, and deploying applications to the cluster

* **CI (Continuous Integration)**: automated testing and image building triggered by code commits
* **CD (Continuous Delivery/[[Deployments]])**: automated delivery of built artifacts to Kubernetes clusters

## GitOps Model

GitOps is the dominant CD pattern for Kubernetes since the cluster state is driven from a Git repository

```
Developer → git push → Git repository
                            │
                    GitOps controller (Argo CD / Flux)
                            │ (sync)
                     Kubernetes cluster
```

* The desired state (YAML manifests, [[Helm]] charts, [[Kustomize]] overlays) lives in Git
* The GitOps controller continuously syncs the cluster state toward the Git-defined desired state
* Any manual [[kubectl]] changes are automatically reverted because here Git is the single source of truth

## Common Pipeline Tools

| Tool | Category | Description |
|---|---|---|
| **[[Argo CD]]** | CD (GitOps) | Kubernetes-native GitOps controller |
| **Flux** | CD (GitOps) | GitOps operator with Helm/Kustomize integration |
| **Tekton** | CI | Kubernetes-native CI/CD pipeline framework |
| **GitHub Actions** | CI | Cloud-hosted CI with Kubernetes deploy actions |
| **Jenkins** | CI | Widely-used CI with Kubernetes plugin support |

## Argo CD

Argo CD is the most widely adopted GitOps CD tool for Kubernetes

* Continuously monitors Git repos and applies changes to the cluster automatically
* Provides a web UI and CLI for viewing application health and sync status
* Supports Helm, Kustomize, Jsonnet, and plain YAML manifests

## Deployment Pipeline Example

```
1. Developer pushes code → triggers CI pipeline
2. CI builds Docker image → pushes to registry
3. CI updates image tag in Git manifests repo
4. Argo CD detects Git change → syncs to cluster
5. Kubernetes applies updated Deployment → rolling update begins
```

## Helm-Based Deployment Pattern

```Bash
# Update image tag in values file via CI
helm upgrade my-app ./charts/my-app \
  --set image.tag=$CI_COMMIT_SHA \
  --namespace production \
  --wait
```

Many teams use Helm for templating and Argo CD for GitOps synchronisation as the two complement each other well.
