**ABAC (Attribute-Based Access Control)** is a Kubernetes authorization mode that grants permissions based on policy files defining attribute-based rules

* One of the earliest authorization mechanisms in Kubernetes, now largely replaced by [[RBAC]]
* Requires a restart of the [[kube-apiserver]] whenever policies change which is a significant operational drawback
* Policy files are flat JSON files stored on the control plane node, not Kubernetes objects

## Enabling ABAC

ABAC is activated via flags on the kube-apiserver

```Bash
--authorization-mode=ABAC
--authorization-policy-file=/etc/kubernetes/abac-policy.json
```

## Policy File Format

Each policy is a JSON object with `apiVersion` and `spec` fields

```JSON
{
  "apiVersion": "abac.authorization.kubernetes.io/v1beta1",
  "kind": "Policy",
  "spec": {
    "user": "alice",
    "namespace": "*",
    "resource": "pods",
    "apiGroup": "*",
    "readonly": false
  }
}
```

* `user`: the authenticated username this policy applies to (use `group` for group-based policies)
* `namespace`: the target namespace; `*` means all namespaces
* `resource`: the API resource (e.g., `pods`, `services`)
* `apiGroup`: the API group; `*` matches all groups
* `readonly`: if `true`, only `GET`, `LIST`, and `WATCH` are allowed

## ABAC vs RBAC

| Aspect | ABAC | RBAC |
|---|---|---|
| Policy storage | Static file on disk | Kubernetes API objects |
| Dynamic updates | Requires API server restart | Applied immediately |
| Auditability | Difficult | [[kubectl]]-native |
| Recommended | No | Yes |

## Why ABAC Is No Longer Recommended

* Policy changes require editing a file on the control plane node and restarting kube-apiserver
* No native tooling for audit or inspection via `kubectl`
* RBAC provides equivalent capability with far better operability and is the standard approach

## Practical Example: Multi-User Policy File

```JSON
{"apiVersion":"abac.authorization.kubernetes.io/v1beta1","kind":"Policy","spec":{"user":"admin","namespace":"*","resource":"*","apiGroup":"*","readonly":false}}
{"apiVersion":"abac.authorization.kubernetes.io/v1beta1","kind":"Policy","spec":{"user":"dev","namespace":"development","resource":"pods","apiGroup":"*","readonly":false}}
```

Each line is a separate JSON policy object; the file is newline-delimited.
