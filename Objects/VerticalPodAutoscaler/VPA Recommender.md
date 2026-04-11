
* Responsible for continuously monitoring resource usage from the Kubernetes [[Metrics Server]] API
* Provides recommendations on optimal CPU and memory values
* It only suggests changes and doesn't modify the Pods directly

## How Recommendations Are Generated

The VPA Recommender runs as a [[Deployments]] and continuously queries the metrics pipeline

1. Reads historical CPU and memory usage from the Metrics Server (or Prometheus)
2. Uses a statistical model (histogram-based) to estimate the 95th percentile resource needs
3. Writes recommendations to the VPA object's `status.recommendation` field

## Recommendation Fields

```YAML
status:
  recommendation:
    containerRecommendations:
      - containerName: app
        lowerBound:
          cpu: "50m"
          memory: "64Mi"
        target:
          cpu: "200m"
          memory: "256Mi"
        upperBound:
          cpu: "1"
          memory: "1Gi"
        uncappedTarget:
          cpu: "200m"
          memory: "256Mi"
```

| Field | Meaning |
|---|---|
| `lowerBound` | Minimum safe resource level |
| `target` | Recommended optimal resources |
| `upperBound` | Maximum useful resources |
| `uncappedTarget` | Recommendation ignoring VPA min/max constraints |

## Commands

```Bash
kubectl describe vpa MY_VPA | grep -A20 "Recommendation"
kubectl get vpa MY_VPA -o jsonpath='{.status.recommendation}'
```


## Viewing Recommendations

```Bash
kubectl describe vpa MY_VPA
# Look for: Status → Recommendation → Container Recommendations
```

Recommendations appear a few minutes after VPA is created once sufficient metrics history exists.

## Recommendation Stability

The Recommender uses a decay factor because recent usage is weighted more than old data. Allow at least 24 hours of traffic before trusting recommendations for production sizing.

## Adjusting Recommender Sensitivity

The Recommender can be configured with flags like `--recommender-interval` (default 1 minute) and `--pod-recommendation-min-cpu-millicores` to tune recommendation frequency and minimum thresholds.
