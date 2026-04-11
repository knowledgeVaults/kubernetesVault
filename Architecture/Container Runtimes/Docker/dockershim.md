[[dockershim]] was the Kubernetes shim layer that translated CRI calls into Docker daemon calls

* Allowed Kubernetes to use Docker as its [[Container Runtime]] before the CRI standard matured
* Lived inside the `[[kubelet]]` binary as a built-in component, not a separate process
* Deprecated in Kubernetes 1.20 and fully removed in Kubernetes 1.24

## Why dockershim Was Removed

Docker was not designed to implement the CRI interface natively

* Docker's architecture introduced unnecessary layers: `kubelet → dockershim → dockerd → [[containerd]] → runc`
* containerd can be used directly via CRI, eliminating the extra hop through `dockerd`
* Maintaining dockershim inside kubelet added significant complexity to the Kubernetes codebase
* Removing it allowed Kubernetes to stay strictly CRI-compliant and reduce surface area

## Migration Path

Clusters running Docker as a runtime needed to migrate to a CRI-compliant runtime after 1.24

* **containerd** is the most common replacement, as Docker itself already uses it internally
* **CRI-O** is another CRI-native alternative, designed specifically for Kubernetes
* Docker-built images remain fully compatible since they conform to the OCI image spec
* The `cri-dockerd` project (community-maintained) provides a CRI shim for Docker if migration is not immediately possible

## Impact on Existing Workloads

Container images and workloads were not affected by the removal of dockershim

* OCI-compliant images built with `docker build` run identically on containerd or CRI-O
* Only the runtime interface layer changed — not the container format itself
* Administrators needed to update node-level runtime configuration in `kubelet` flags (`--container-runtime-endpoint`)

## Verifying Runtime After Migration

After migrating away from dockershim, confirm the node uses containerd

```Bash
kubectl get node NODE_NAME -o jsonpath='{.status.nodeInfo.containerRuntimeVersion}'
# Expected output: containerd://1.x.x

# On the node itself
crictl info | grep runtimeType
```
