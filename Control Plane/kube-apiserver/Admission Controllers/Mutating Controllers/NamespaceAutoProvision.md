`NamespaceAutoProvision` is a **mutating [[Admission Controllers]]** that automatically creates a namespace when a resource is submitted to a namespace that does not yet exist

* Deprecated and disabled by default in modern Kubernetes versions
* Was useful in development environments where namespaces were not pre-created before deploying resources
* Replaced by more explicit workflows: operators are expected to create namespaces before deploying resources into them

## Behavior

When enabled, `NamespaceAutoProvision` intercepts `CREATE` requests for namespaced resources

* Checks whether the target namespace exists in the cluster
* If the namespace does not exist, the controller automatically creates it before allowing the resource creation to proceed
* The auto-created namespace has no labels, resource quotas, or limit ranges (It's a bare namespace)

## Why It Was Deprecated

Auto-provisioning namespaces silently creates infrastructure without an explicit operator action

* It bypasses intentional namespace lifecycle management
* Resource quotas and network policies configured at the namespace level are not applied to auto-created namespaces
* In production clusters, all namespaces should be explicitly created via IaC ([[Helm]], [[Kustomize]], Terraform) with proper configuration

## Alternative: NamespaceExists

The `NamespaceExists` **validating** controller is the counterpart that **rejects** requests targeting non-existent namespaces

* This is the preferred behavior in production (Fail loudly rather than silently create infrastructure)

## Enabling/Disabling

Admission controllers are configured via the `--enable-admission-plugins` and `--disable-admission-plugins` flags on the [[kube-apiserver]]

```Bash
# Check which admission plugins are enabled
kube-apiserver --help | grep admission
kubectl exec -n kube-system kube-apiserver-NODE -- kube-apiserver --help 2>&1 | grep enable-admission
```

To enable: add `NamespaceAutoProvision` to `--enable-admission-plugins`

## Example: Checking If It Is Enabled

```Bash
kubectl exec -n kube-system kube-apiserver-NODE --   kube-apiserver --help 2>&1 | grep -i NamespaceAutoProvision
```

In [[kubeadm]] clusters, check `/etc/kubernetes/manifests/kube-apiserver.yaml` for the `--enable-admission-plugins` flag.
