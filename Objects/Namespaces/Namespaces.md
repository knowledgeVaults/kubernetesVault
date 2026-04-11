
**Namespaces** are virtual clusters within a Kubernetes cluster that logically isolate resources, providing scope for names, access control, and resource quotas

* A resource name must be unique within a namespace but can repeat across namespaces
* Most resources (Pods, Services, Deployments) are namespaced; some are cluster-scoped (Nodes, PVs, ClusterRoles)
* [[RBAC]] policies can be scoped to namespaces using [[Role]] and [[RoleBindings]]

## Manifest

```YAML
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    environment: production
    team: platform
```

## Default Namespaces

| Namespace | Purpose |
|---|---|
| `default` | Workloads created without a namespace specification |
| `kube-system` | Kubernetes control plane components ([[etcd]], [[CoreDNS]], [[kube-proxy]]) |
| `kube-public` | Publicly readable ConfigMaps; rarely used |
| `kube-node-lease` | Node heartbeat Lease objects |

## Namespace Commands

```Bash
kubectl create namespace production
kubectl get namespaces
kubectl describe namespace production
kubectl delete namespace production   # Deletes all resources inside

# Set default namespace for current context
kubectl config set-context --current --namespace=production

# List resources in a namespace
kubectl get pods -n production
kubectl get all -n production

# List across all namespaces
kubectl get pods --all-namespaces
kubectl get pods -A
```

## Resource Isolation

Namespaces do NOT provide network isolation by default

* Pods in different namespaces can communicate unless [[Network Policies]] objects restrict it
* Use `NetworkPolicy` in conjunction with namespaces for true workload isolation

## Cross-Namespace DNS

Services in other namespaces are reachable via `SERVICE.NAMESPACE.svc.cluster.local`

```Bash
curl http://my-service.production.svc.cluster.local:80
```

## Namespace-Scoped vs Cluster-Scoped Resources

```Bash
# List all namespace-scoped resources
kubectl api-resources --namespaced=true

# List all cluster-scoped resources
kubectl api-resources --namespaced=false
```
