`NamespaceExists` is a **validating [[Admission Controllers]]** that rejects requests targeting a namespace that does not exist in the cluster

* Enabled by default in Kubernetes clusters
* Acts as a safeguard to prevent accidental resource creation in non-existent namespaces
* Works in contrast to `NamespaceAutoProvision`, which would silently create the namespace instead

## Behavior

When a client submits a `CREATE` request for a namespaced resource, `NamespaceExists` checks whether the target namespace is present

* If the namespace exists and is active (`Active` phase), the request proceeds
* If the namespace does not exist, the admission controller returns a `404 Not Found` error
* If the namespace exists but is in `Terminating` phase, requests to create new resources in it are also rejected

## Namespace Phases

| Phase | Description |
|---|---|
| `Active` | Namespace is healthy and accepts resources |
| `Terminating` | Namespace is being deleted; new resources are blocked |

## Interaction with NamespaceLifecycle

In modern Kubernetes, `NamespaceExists` behavior is merged into the **`NamespaceLifecycle`** admission plugin

* `NamespaceLifecycle` handles both the "namespace must exist" check and the "no new resources in terminating namespace" check
* `NamespaceLifecycle` cannot be disabled (It's a mandatory admission plugin
* The standalone `NamespaceExists` controller is largely historical; `NamespaceLifecycle` supersedes it

## Practical Impact

```Bash
# Submitting a resource to a non-existent namespace
kubectl apply -f pod.yaml -n non-existent-namespace
# Error: namespaces "non-existent-namespace" not found

# Fix: create the namespace first
kubectl create namespace non-existent-namespace
kubectl apply -f pod.yaml -n non-existent-namespace
```

## Why This Matters

Without this guard, a typo in the namespace field would silently create resources in an unintended location if `NamespaceAutoProvision` were active, making auditing and access control unreliable.
