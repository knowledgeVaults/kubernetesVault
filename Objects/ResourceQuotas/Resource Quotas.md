A **[[Resource Quotas]]** limits the total amount of resources that can be consumed across all objects in a namespace

* Prevents a single team or application from exhausting cluster-wide resources
* Applied by the `ResourceQuota` [[Admission Controllers]] where requests exceeding quotas are rejected
* Covers compute resources (CPU, memory), object counts (pods, services), and storage

## ResourceQuota Manifest

```YAML
apiVersion: v1
kind: ResourceQuota
metadata:
  name: team-quota
  namespace: team-alpha
spec:
  hard:
    requests.cpu: "4"
    requests.memory: "8Gi"
    limits.cpu: "8"
    limits.memory: "16Gi"
    pods: "20"
    services: "10"
    secrets: "50"
    configmaps: "50"
    persistentvolumeclaims: "10"
    services.nodeports: "0"         # Block NodePort services
    services.loadbalancers: "2"
```

## Quota Fields

| Field | Description |
|---|---|
| `requests.cpu` | Total CPU requests across all pods |
| `limits.cpu` | Total CPU limits across all pods |
| `requests.memory` | Total memory requests |
| `limits.memory` | Total memory limits |
| `pods` | Maximum number of pods |
| `services` | Maximum number of services |
| `services.nodeports` | Maximum number of [[NodePort]] services (0 = blocked) |
| `persistentvolumeclaims` | Maximum number of PVCs |
| `count/deployments.apps` | Maximum number of Deployments |

## Quota Interaction with LimitRanges

When a namespace has a ResourceQuota for CPU/memory, **all pods must define resource requests and limits** because otherwise they are rejected. Combine ResourceQuota with a [[Limit Ranges]] to provide defaults automatically.

## Checking Usage

```Bash
kubectl get resourcequota -n NAMESPACE
kubectl describe resourcequota team-quota -n team-alpha
# Shows: Name, Namespace, Resource, Used, Hard columns
```

## Scope Selectors

```YAML
scopeSelector:
  matchExpressions:
    - operator: In
      scopeName: PriorityClass
      values: [high-priority]
```

Apply a quota only to pods with a specific priority class (Useful for reserving resources for critical workloads)
