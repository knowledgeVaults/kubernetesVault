**Identity Services** in Kubernetes are external authentication providers that issue tokens or certificates validated by the [[kube-apiserver]]

* Kubernetes itself does not have a built-in user database (External identity providers handle human user authentication)
* Service account tokens handle machine-to-machine authentication within the cluster
* The API server is configured to trust one or more external identity providers via authentication mode flags

## OIDC (OpenID Connect)

OIDC is the primary way to integrate external identity providers with the Kubernetes API server

* Users authenticate to the IdP (Dex, Keycloak, Okta, Azure AD, Google) and receive a JWT ID token
* The API server validates the JWT using the IdP's public keys
* Claims from the token (e.g., `sub`, `email`, `groups`) are mapped to Kubernetes username and groups

### kube-apiserver OIDC Flags

```Bash
--oidc-issuer-url=https://accounts.google.com
--oidc-client-id=my-k8s-client
--oidc-username-claim=email
--oidc-groups-claim=groups
--oidc-ca-file=/etc/kubernetes/pki/oidc-ca.crt
```

## LDAP / Active Directory

Kubernetes does not natively speak LDAP (an OIDC proxy like **Dex** bridges LDAP/AD to the API server)

* Dex authenticates users against LDAP and issues OIDC tokens
* The API server is configured to trust Dex as the OIDC issuer
* Group membership from LDAP can be mapped to Kubernetes [[RBAC]] `Group` subjects

## Webhook Token Authentication

An external webhook can be used to validate bearer tokens

```Bash
--authentication-token-webhook-config-file=/etc/kubernetes/webhook-authn.yaml
```

* The API server calls the webhook with the token; the webhook responds with the authenticated user identity
* Useful for custom token formats not supported by OIDC

## Service Account Tokens

Service accounts use projected tokens signed by the API server's signing key

* Tokens are audience-bound, time-limited, and automatically rotated via the `TokenRequest` API
* Mounted into pods automatically at `/var/run/secrets/kubernetes.io/[[Service Accounts]]/token`
