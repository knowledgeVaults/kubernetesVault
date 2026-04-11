**HPA Statuses** describe the current operational state of a [[Horizontal Pod Autoscaler]] object, including scaling decisions and metric readings

* Visible in `.status` of the HPA object
* Used to diagnose why an HPA is or is not scaling as expected

## Status Fields

```YAML
status:
  currentReplicas: 3
  desiredReplicas: 5
  currentMetrics:
    - type: Resource
      resource:
        name: cpu
        current:
          averageUtilization: 78
          averageValue: "390m"
  conditions:
    - type: AbleToScale
      status: "True"
      reason: SucceededScale
    - type: ScalingActive
      status: "True"
      reason: ValidMetricFound
    - type: ScalingLimited
      status: "False"
      reason: DesiredWithinRange
  lastScaleTime: "2024-01-01T00:00:00Z"
```

## Conditions

| Condition | Meaning |
|---|---|
| `AbleToScale` | Whether the HPA can currently scale (True/False) |
| `ScalingActive` | Whether scaling is currently active (metrics are available) |
| `ScalingLimited` | Whether the desired replica count is being capped by min/max bounds |

## Common Status Reasons

| Reason | Condition | Explanation |
|---|---|---|
| `FailedGetScale` | `AbleToScale=False` | Cannot read current replica count from the target |
| `UnableToComputeReplicaCount` | `ScalingActive=False` | Metric unavailable (metrics-server not running) |
| `TooFewReplicas` | `ScalingLimited=True` | Desired replicas hit `minReplicas` floor |
| `TooManyReplicas` | `ScalingLimited=True` | Desired replicas hit `maxReplicas` ceiling |

## Diagnosing HPA Issues

```Bash
kubectl describe hpa MY_HPA -n NAMESPACE
# Look at: Conditions, Events, Metrics section

kubectl get hpa MY_HPA -n NAMESPACE
# Shows TARGETS column: current/target utilization
```

## Cooldown / Stabilization

The HPA applies stabilization windows to prevent thrashing

* `scaleDown.stabilizationWindowSeconds` (default 300s): prevents rapid scale-down after a spike subsides
* `scaleUp.stabilizationWindowSeconds` (default 0s): scale up reacts immediately to load increases
