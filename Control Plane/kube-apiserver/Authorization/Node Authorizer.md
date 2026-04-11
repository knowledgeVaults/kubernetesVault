The **Node Authorizer** is a special-purpose authorization mode in Kubernetes that grants kubelets permission to access only the resources needed to run pods assigned to their node

* Designed to enforce the principle of least privilege for [[kubelet]] access
* A kubelet may only read Secrets, ConfigMaps, PersistentVolumeClaims, and other resources that are mounted by pods scheduled to that specific node
* Without the Node Authorizer, a compromised kubelet could read Secrets for pods on any node

## How It Works

The Node Authorizer evaluates requests from kubelets identified as `system:node:NODE_NAME` in the `system:nodes` group

* Kubelets authenticate using TLS certificates with `CN=system:node:NODE_NAME` and `O=system:nodes`
* The authorizer checks whether the requested resource is actually needed by a pod scheduled to that node
* Access is granted only when the relationship can be verified via the [[kube-apiserver]]'s internal graph

## Node Authorizer + NodeRestriction Admission Plugin

The Node Authorizer works in conjunction with the `NodeRestriction` [[Admission Controllers]] for full enforcement

* **Node Authorizer**: controls what a kubelet is allowed to read (e.g., only Secrets used by its pods)
* **NodeRestriction**: prevents a kubelet from modifying labels or taints on nodes other than itself

Together they implement the **Node Authorization Policy**:

| Resource | Access Granted |
|---|---|
| `[[Secrets]]` | Only if mounted by a pod on this node |
| `[[Config Maps]]` | Only if mounted by a pod on this node |
| `[[PersistentVolumeClaims]]` | Only if mounted by a pod on this node |
| `[[PersistentVolumes]]` | Only if bound to a PVC used by this node |
| `Node` (self) | Read and status update for own node object |

## Enabling Node Authorization

```Bash
--authorization-mode=Node,RBAC
```

The Node mode is typically combined with [[RBAC]] — Node handles kubelet access, RBAC handles everything else.

## Verifying Node Authorization Is Active

```Bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep authorization-mode
# Should include: --authorization-mode=Node,RBAC
```

If `Node` is missing from the authorization mode list, kubelets fall back to RBAC-only access, losing the node-scoped restrictions.
