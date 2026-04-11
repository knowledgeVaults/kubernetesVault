**Scheduler Profiles** allow a single [[kube-scheduler]] binary to run multiple logical schedulers, each with its own plugin configuration, under different names

* Introduced to replace the need to run multiple separate scheduler binaries
* Each profile has a unique `schedulerName`; Pods select a profile via `spec.schedulerName`
* Profiles share the same scheduler process and cache — reducing resource overhead

## KubeSchedulerConfiguration

Profiles are defined in a `KubeSchedulerConfiguration` object passed via `--config` to the scheduler

```YAML
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
  - schedulerName: default-scheduler
    plugins:
      score:
        disabled:
          - name: NodeResourcesBalancedAllocation
        enabled:
          - name: MyCustomScorePlugin

  - schedulerName: high-priority-scheduler
    plugins:
      filter:
        disabled:
          - name: TaintToleration
      score:
        enabled:
          - name: ImageLocality
```

## Using a Custom Profile in a Pod

```YAML
spec:
  schedulerName: high-priority-scheduler
  containers:
    - name: my-container
      image: my-image
```

If `schedulerName` does not match any running scheduler, the Pod remains `Pending` indefinitely.

## Plugin Extension Points

Each profile can enable, disable, or reorder plugins at every extension point

| Extension Point | Purpose |
|---|---|
| `PreFilter` | Pre-checks and cycle data setup |
| `Filter` | Node eligibility (remove unfit nodes) |
| `PostFilter` | Called if no nodes passed Filter (preemption) |
| `PreScore` | Setup before scoring |
| `Score` | Rank eligible nodes (0–100 per plugin) |
| `Reserve` | Pessimistic reserve resources |
| `Bind` | Write the scheduling decision to the [[kube-apiserver]] |

## Listing Active Profiles

```Bash
kubectl get pod -n kube-system kube-scheduler-NODENAME -o yaml | grep -A5 command
```

## Default Plugins Reference

```Bash
# View the default scheduler's active plugins
kubectl exec -n kube-system kube-scheduler-NODE -- \
  kube-scheduler --help 2>&1 | grep -i plugin
```

Profiles make it easy to experiment with plugin combinations without deploying a separate scheduler binary.
