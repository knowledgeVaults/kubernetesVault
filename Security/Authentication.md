
**Authentication** in Kubernetes establishes the identity of a caller before any authorization or admission checks run

* Managed exclusively by the [[kube-apiserver]] (No separate authentication service)
* Multiple authentication plugins can be active simultaneously; the first to succeed wins
* Kubernetes has no built-in user database where the human users authenticate via external mechanisms

## Authentication Methods

| Method                        | Use Case                                                            |
| ----------------------------- | ------------------------------------------------------------------- |
| **X.509 Client Certificates** | Component-to-component ([[kubelet]], controller-manager, scheduler) |
| **Service Account Tokens**    | In-cluster pods authenticating to the API server                    |
| **OIDC Tokens**               | Human users via external IdP (Okta, Azure AD, Dex, Keycloak)        |
| **Static Token File**         | Development/testing only (Never in production)                      |
| **Webhook Token**             | Custom external authentication service                              |

## Certificate-Based Authentication

Kubernetes components authenticate using TLS certificates

* `CN` (Common Name) becomes the username: `CN=system:node:NODE_NAME`
* `O` (Organization) becomes the group: `O=system:nodes`
* Certificates are signed by the cluster's CA (`/etc/kubernetes/pki/ca.crt`)

```Bash
# Inspect a certificate's CN and O fields
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -subject -issuer
```

## Service Account Token Authentication

Pods automatically receive a projected service account token mounted at `/var/run/secrets/kubernetes.io/Service Accounts/token`

* Tokens are short-lived (default 1 hour) and automatically rotated by the kubelet
* Audience-bound: only valid for `kubernetes.default.svc`
* Created via the `TokenRequest` API and is no longer stored as Secrets by default (Kubernetes 1.24+)

## OIDC Configuration

```Bash
# kube-apiserver flags for OIDC
--oidc-issuer-url=https://accounts.google.com
--oidc-client-id=my-k8s-app
--oidc-username-claim=email
--oidc-groups-claim=groups
```

## Checking Identity

```Bash
kubectl auth whoami
kubectl get pods --v=9 2>&1 | grep "Request Headers"
```

## Disabling Anonymous Access

```Bash
# kube-apiserver flag
--anonymous-auth=false
```

Without anonymous access, any request without credentials returns `401 Unauthorized` immediately.
