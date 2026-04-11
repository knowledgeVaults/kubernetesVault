A **[[Limit Ranges]]** enforces default and maximum resource constraints per container, pod, or [[PersistentVolumeClaims]] within a specific namespace

* Automatically injects default resource requests and limits into containers that don't specify them
* Prevents individual containers or pods from consuming unbounded resources in the namespace
* Applied by the `LimitRanger` [[Admission Controllers]]

## LimitRange Manifest

```YAML
apiVersion: v1
kind: LimitRange
metadata:
  name: resource-limits
  namespace: development
spec:
  limits:
    - type: Container
      default:
        cpu: "500m"
        memory: "256Mi"
      defaultRequest:
        cpu: "100m"
        memory: "64Mi"
      max:
        cpu: "2"
        memory: "2Gi"
      min:
        cpu: "50m"
        memory: "32Mi"
    - type: Pod
      max:
        cpu: "4"
        memory: "4Gi"
    - type: PersistentVolumeClaim
      max:
        storage: "10Gi"
      min:
        storage: "1Gi"
```

## LimitRange Fields

| Field | Applies To | Meaning |
|---|---|---|
| `default` | Container | Default limit injected if container omits limits |
| `defaultRequest` | Container | Default request injected if container omits requests |
| `max` | Container/Pod/PVC | Maximum allowed value; requests/limits exceeding this are rejected |
| `min` | Container/Pod/PVC | Minimum required value; requests below this are rejected |

## Effect on New Pods

When a Pod is created in a namespace with a LimitRange:
* Containers without `resources.limits` receive the LimitRange `default` values
* Containers without `resources.requests` receive the LimitRange `defaultRequest` values
* If a container's values exceed `max`, the admission is rejected with a validation error

## Commands

```Bash
kubectl get limitrange -n NAMESPACE
kubectl describe limitrange resource-limits -n development
```

## LimitRange vs [[Resource Quotas]]

LimitRange sets per-container/pod defaults and maximums; ResourceQuota sets total namespace-wide limits. Use both together: LimitRange ensures every pod has resource specs; ResourceQuota limits the aggregate.
