## Principle of Least Privilege

Grant only the minimum [[RBAC]] permissions required for each workload and user

* Prefer [[Role]] + [[RoleBindings]] over [[Cluster Roles]] + [[Cluster Role Bindings]] unless cluster-wide access is genuinely needed
* Audit RBAC bindings regularly (Remove stale permissions for decommissioned service accounts)
* Avoid using the `default` service account for application pods (Create a dedicated service account per workload)

## Pod Security

Define security contexts at the pod and container level to reduce the attack surface

* Set `runAsNonRoot: true` and specify a non-zero `runAsUser` for all containers
* Set `readOnlyRootFilesystem: true` whenever the application does not need to write to the container filesystem
* Drop all Linux capabilities and add back only what is strictly required (`drop: ["ALL"]`)
* Use `allowPrivilegeEscalation: false` to prevent containers from gaining more privileges than their parent process

## Secrets Management

Never store secrets in plaintext inside ConfigMaps, environment variables in manifests, or container images

* Enable Secrets encryption at rest in [[etcd]] via [[Encryption Configuration]]
* Prefer external secrets management tools (HashiCorp Vault, AWS Secrets Manager) with the External Secrets Operator
* Rotate secrets regularly and use short-lived tokens wherever possible (projected service account tokens)

## Network Security

Enforce network segmentation using [[Network Policies]] resources

* Apply a default-deny-all baseline per namespace, then allow only necessary flows
* Enable mutual TLS (mTLS) between services via a service mesh (Istio, Linkerd) for encrypted in-cluster traffic

## [[Image Security]]

* Scan container images for vulnerabilities in CI pipelines (Trivy, Grype) before pushing to production
* Use the [[ImagePolicyWebhook]] [[Admission Controllers]] to block images from untrusted registries
* Pin image tags to specific digests (`image@sha256:...`) in production to prevent tag mutation attacks

## Audit Logging

Enable the Kubernetes [[kube-apiserver]] audit log to track all requests

* Define an `AuditPolicy` object and pass it via `--audit-policy-file` to the API server
* Log at `Metadata` level for most resources; log `RequestResponse` for Secrets and sensitive resources
* Ship audit logs to a SIEM or log aggregation system for retention and analysis
