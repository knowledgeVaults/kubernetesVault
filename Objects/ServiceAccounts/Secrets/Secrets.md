**[[Service Accounts]] Secrets** are Kubernetes [[Secrets]] objects of type `kubernetes.io/service-account-token` that contain a long-lived bearer token for authenticating as a ServiceAccount

* Automatically created by the ServiceAccount controller for each SA in Kubernetes versions prior to 1.24
* In Kubernetes 1.24+, long-lived SA secrets are **no longer auto-generated** because tokens are issued on-demand via the `TokenRequest` API as short-lived projected tokens
* Manual SA Secrets are still supported when long-lived tokens are explicitly required (e.g., external CI/CD systems)

## Auto-Created SA Secrets (Pre-1.24)

```YAML
apiVersion: v1
kind: Secret
metadata:
  name: my-sa-token-xxxxx
  namespace: default
  annotations:
    kubernetes.io/service-account.name: my-sa
type: kubernetes.io/service-account-token
data:
  token: BASE64_ENCODED_JWT
  ca.crt: BASE64_ENCODED_CA
  namespace: ZGVmYXVsdA==
```

## Manually Creating a Long-Lived SA Token (Post-1.24)

```YAML
apiVersion: v1
kind: Secret
metadata:
  name: my-sa-token
  namespace: default
  annotations:
    kubernetes.io/service-account.name: my-sa
type: kubernetes.io/service-account-token
```

The [[kube-apiserver]] automatically populates the `token`, `ca.crt`, and `namespace` fields.

## On-Demand Short-Lived Tokens (Recommended)

```Bash
# Create a token valid for 1 hour
kubectl create token my-sa --duration=3600s -n default

# Decode the token to inspect claims
kubectl create token my-sa | jq -R 'split(".") | .[1] | @base64d | fromjson'
```

## Projected Service Account Volume

Pods automatically receive a projected token at pod startup

```YAML
volumes:
  - name: sa-token
    projected:
      sources:
        - serviceAccountToken:
            expirationSeconds: 3600
            path: token
```

This is the preferred method because tokens are automatically rotated by the [[kubelet]].

## Revoking a Long-Lived Token

```Bash
kubectl delete secret my-sa-token -n default
```

Deleting the Secret immediately invalidates the token. For projected tokens, rotation is automatic and no manual revocation is needed.
