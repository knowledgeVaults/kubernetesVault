**Authentication** is the first gate in the Kubernetes API request pipeline that verifies the identity of the caller before authorization and admission checks run

* Handled entirely by the **[[kube-apiserver]]**; there is no separate authentication service
* Kubernetes does not have a built-in user database (External mechanisms provide credentials)
* Multiple authentication methods can be active simultaneously; the first to succeed wins

## Authentication Methods

| Method | Description | Use Case |
|---|---|---|
| **X.509 Client Certificates** | TLS cert with `CN` as username, `O` as group | Component-to-component ([[kubelet]], controller-manager) |
| **Static Token File** | CSV file of pre-defined tokens | Development/testing only |
| **Service Account Tokens** | JWT signed by the API server | In-cluster pods |
| **OIDC Tokens** | JWTs from external identity providers | Human user login via SSO |
| **Webhook Token Authentication** | External HTTP service validates tokens | Custom auth systems |
| **Authenticating Proxy** | Reverse proxy sets identity headers | Enterprise SSO integration |

## Certificate Authentication

kubelets authenticate using certificates with `CN=system:node:NODE_NAME` and `O=system:nodes`

```Bash
# Inspect a certificate to see CN and O fields
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -subject
```

## Service Account Authentication

Pods receive a projected service account token at `/var/run/secrets/kubernetes.io/[[Service Accounts]]/token`

* Token is audience-bound (`aud: kubernetes.default.svc`) and expires after 1 hour by default
* Automatically rotated by the kubelet before expiry

## Anonymous Access

Unauthenticated requests are assigned the identity `system:anonymous` in the `system:unauthenticated` group

* Anonymous access is enabled by default but almost all meaningful operations are denied by [[RBAC]]
* Disable with `--anonymous-auth=false` if not required

## Checking Your Identity

```Bash
kubectl auth whoami
# Or
kubectl get pods --v=9 2>&1 | grep "Response Status"
```
