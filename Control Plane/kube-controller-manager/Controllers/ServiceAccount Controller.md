The **[[Service Accounts]] Controller** ensures that a `default` ServiceAccount exists in every namespace and manages the associated token secrets

* Watches `Namespace` objects and creates a `default` ServiceAccount automatically in each new namespace
* In Kubernetes versions prior to 1.24, also created a long-lived token [[Secrets]] for each ServiceAccount automatically
* In 1.24+, tokens are created on-demand via the `TokenRequest` API (projected volumes) rather than as persistent Secrets

## Default ServiceAccount

Every namespace receives a `default` ServiceAccount created by this controller

```YAML
apiVersion: v1
kind: ServiceAccount
metadata:
  name: default
  namespace: my-namespace
```

* Pods without an explicit `serviceAccountName` are assigned the `default` ServiceAccount automatically
* The `default` ServiceAccount has no [[RBAC]] permissions by default as it can only access resources granted by explicit Role/[[Cluster Roles]] bindings

## Token Lifecycle (Post-1.24)

Since Kubernetes 1.24, the `LegacyServiceAccountTokenNoAutoGeneration` feature gate disabled automatic Secret creation

* Tokens are now issued as **projected service account tokens** via the `TokenRequest` API
* Projected tokens are: audience-bound, time-limited (default 1 hour, auto-rotated), and not stored as Secrets
* Mounted automatically into pods at `/var/run/secrets/kubernetes.io/serviceaccount/token`

## Legacy Token Secrets (Pre-1.24)

Prior to 1.24, the controller created a `kubernetes.io/service-account-token` Secret per ServiceAccount

* These tokens had no expiry and were a security risk if leaked
* Existing legacy tokens continue to function but should be migrated to projected tokens

## Creating a Manual Token (Post-1.24)

```YAML
apiVersion: v1
kind: Secret
metadata:
  name: my-sa-token
  annotations:
    kubernetes.io/service-account.name: my-serviceaccount
type: kubernetes.io/service-account-token
```

The [[kube-apiserver]] populates the token field automatically.

## Verifying Default ServiceAccount

```Bash
kubectl get serviceaccount default -n NAMESPACE -o yaml
# automountServiceAccountToken defaults to true
# Override per pod:
# spec.automountServiceAccountToken: false
```

Disable token automounting on the default ServiceAccount to reduce the attack surface for pods that do not need API access.
