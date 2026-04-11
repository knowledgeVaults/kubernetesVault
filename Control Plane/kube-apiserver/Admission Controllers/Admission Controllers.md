**Admission Controllers** are pluggable code modules that intercept API requests to the [[kube-apiserver]] after authentication and authorization, but before the object is persisted to [[etcd]]

* Can **mutate** (modify) the incoming object and/or **validate** (accept or reject) it
* Run in two sequential phases: mutating first, then validating
* Built-in controllers are compiled into kube-apiserver; external logic uses webhook-based controllers

## Admission Phases

```
Client → Authentication → Authorization → Mutating Admission → Schema Validation → Validating Admission → etcd
```

### 1. Mutating Admission Phase

All mutating controllers run in sequence; each receives the output of the previous one

* Controllers may add default values, inject sidecars, or modify labels
* Example: `DefaultStorageClass` injects a default `storageClassName` into PVCs

### 2. Validating Admission Phase

All validating controllers run (possibly in parallel); any rejection fails the entire request

* Controllers only accept or reject — they cannot further mutate the object
* Example: `NamespaceLifecycle` rejects resources in terminating namespaces

## Enabling and Disabling Controllers

```Bash
# kube-apiserver flags
--enable-admission-plugins=NodeRestriction,NamespaceLifecycle
--disable-admission-plugins=DefaultStorageClass
```

In [[kubeadm]] clusters, edit `/etc/kubernetes/manifests/kube-apiserver.yaml`.

## Built-in Controllers (Always-On)

Some controllers are always active and cannot be disabled:

* `NamespaceLifecycle`: blocks resources in non-existent or terminating namespaces
* `LimitRanger`: enforces `[[Limit Ranges]]` policies
* `[[Service Accounts]]`: auto-assigns ServiceAccounts and mounts tokens

## Viewing Active Controllers

```Bash
kubectl exec -n kube-system kube-apiserver-NODE -- \
  kube-apiserver -h 2>&1 | grep enable-admission-plugins
```

## Checking Which Plugins Are Enabled

```Bash
kubectl exec -n kube-system kube-apiserver-NODE -- \
  kube-apiserver -h 2>&1 | grep enable-admission

# Or check the static pod manifest directly
grep admission /etc/kubernetes/manifests/kube-apiserver.yaml
```
