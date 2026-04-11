
**Kubernetes [[Security Primitives]]** are the built-in mechanisms that collectively secure a cluster, control access, and protect workloads from the infrastructure level down to the container level

## Authentication

Verifies *who* a caller is before any operation is permitted

| Primitive | Description |
|---|---|
| X.509 Certificates | TLS client certs; `CN` = username, `O` = group |
| Bearer Tokens | Static token file or service account JWT tokens |
| ServiceAccounts | Machine identities for in-cluster pods |
| External Providers | OIDC (Okta, Azure AD, Dex); Webhook Token auth |

## Authorization

Determines *what* an authenticated caller is allowed to do

| Primitive       | Description                                                        |
| --------------- | ------------------------------------------------------------------ |
| [[RBAC]]        | Role-based permissions via `Role`, [[Cluster Roles]], and bindings |
| ABAC            | File-based attribute policies (deprecated)                         |
| Node Authorizer | Restricts [[kubelet]] access to its own node's resources           |
| Webhook         | Delegates decisions to external policy engines (OPA)               |

## Admission Control

Intercepts and validates/mutates requests before they are persisted to [[etcd]]

| Primitive                        | Description                                                           |
| -------------------------------- | --------------------------------------------------------------------- |
| Mutating Admission Controllers   | Inject defaults, sidecars; e.g., `DefaultStorageClass`                |
| Validating Admission Controllers | Enforce constraints; e.g., `NamespaceLifecycle`, `ImagePolicyWebhook` |
| ValidatingAdmissionPolicy        | CEL-based policies evaluated in-process (Kubernetes 1.30+)            |

## Network Security

| Primitive | Description |
|---|---|
| NetworkPolicies | L3/L4 pod-to-pod traffic rules |
| TLS everywhere | All [[kube-apiserver]], etcd, and kubelet communication uses mTLS |
| CNI plugins | CNI plugins (Calico, Cilium) enforce [[Network Policies]] rules |

## Secrets and Data Protection

| Primitive | Description |
|---|---|
| Secrets | Base64-encoded key-value store for sensitive data |
| [[Encryption Configuration]] | Encrypts Secrets at rest in etcd |
| External Secrets Operator | Syncs secrets from Vault, AWS Secrets Manager, etc. |

## Pod and Container Security

| Primitive | Description |
|---|---|
| SecurityContext | Controls UID/GID, capabilities, privilege escalation |
| Pod Security Admission | Namespace-level enforcement of security standards (baseline, restricted) |
| seccomp profiles | Restrict which syscalls containers can make |
