**Authorization** in Kubernetes determines whether an authenticated caller is permitted to perform a requested action on a specific resource

* Runs after authentication (The caller must already be identified before authorization checks apply)
* The [[kube-apiserver]] evaluates one or more authorization modules in the order they are configured
* The first module to return an explicit `Allow` or `Deny` wins; if all modules return `NoOpinion`, the request is denied

## Authorization Modes

Set via `--authorization-mode` on the kube-apiserver (comma-separated, evaluated in order)

```Bash
--authorization-mode=Node,RBAC
```

| Mode | Description |
|---|---|
| `Node` | Restricts [[kubelet]] access to resources needed by its own pods |
| `[[RBAC]]` | Role-based access using `Role`, `[[Cluster Roles]]`, and their bindings |
| `ABAC` | File-based attribute policies (requires API server restart to update) |
| `Webhook` | Delegates decisions to an external HTTP service |
| `AlwaysAllow` | Permits everything — for testing only |
| `AlwaysDeny` | Denies everything — for testing only |

## Checking Authorization

```Bash
# Can the current user perform an action?
kubectl auth can-i get pods -n default

# Impersonate another user
kubectl auth can-i get secrets --as=alice -n production

# Check all permissions for a user
kubectl auth can-i --list --as=alice -n default
```

## SubjectAccessReview

The API for programmatically checking authorization decisions

```YAML
apiVersion: authorization.k8s.io/v1
kind: SubjectAccessReview
spec:
  user: alice
  groups: ["developers"]
  resourceAttributes:
    namespace: default
    verb: delete
    resource: deployments
```

```Bash
kubectl create -f sar.yaml -o yaml
# Check status.allowed in the response
```

## Open Policy Agent (Webhook)

OPA can act as a Webhook authorization backend for policy-as-code enforcement

* Policies written in Rego are evaluated per request
* Allows fine-grained rules beyond standard RBAC (e.g., deny deployments to production namespaces on Fridays)
