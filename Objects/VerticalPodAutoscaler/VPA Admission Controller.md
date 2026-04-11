
* Intervenes the Pod creation process and uses the recommendations from the Recommender to then mutate the Pods to apply the new memory and CPU values at startup
* Ensures that the Pods start with the correct resources limits and requests

```
VPA Recommender --> VPA Updater --> Delete Pods --> VPA Admissioon Controller --> Intercepts Pod Creations --> Vertically Scaled Pods
```

## How the VPA [[Admission Controllers]] Works

The VPA Admission Controller is a **mutating admission webhook** that intercepts pod `CREATE` requests

1. Pod creation is triggered (by a [[Deployments]], [[Stateful Sets]], or Job controller)
2. [[kube-apiserver]] calls the VPA Admission Controller webhook
3. The webhook fetches the latest recommendation for the matching VPA object
4. It mutates the pod's `resources.requests` and `resources.limits` based on the recommendation
5. The pod is created with the updated resources since the user-specified values are overridden

```
VPA Recommender → stores recommendation in VPA status
VPA Updater     → evicts out-of-range pods
                       ↓
Pod recreated → kube-apiserver → VPA Admission Controller webhook
                                   → mutates resources → pod starts with correct sizing
```

## Resource Mutation

```YAML
# Before VPA mutation (user-defined)
resources:
  requests:
    cpu: "100m"
    memory: "64Mi"

# After VPA mutation (from recommendation)
resources:
  requests:
    cpu: "250m"
    memory: "200Mi"
```

## Commands

```Bash
# Check if VPA admission controller webhook is registered
kubectl get mutatingwebhookconfigurations | grep vpa

# View VPA objects
kubectl get vpa -A
```


## Verifying VPA Admission Controller

```Bash
kubectl get mutatingwebhookconfigurations | grep vpa
# Should show: vpa-webhook-config

kubectl get pods -n kube-system | grep vpa
# Should show: vpa-admission-controller, vpa-recommender, vpa-updater
```

## Conflict with HPA

Do not run HPA and VPA in `Auto` mode on the same workload targeting the same resource metric (CPU or memory). They will fight each other. Use VPA in `Initial` mode, or use KEDA for event-driven scaling instead.
