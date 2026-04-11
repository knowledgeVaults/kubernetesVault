The **Recreate** [[Deployments]] strategy terminates all existing Pods before creating Pods running the new version

* Results in a brief period of downtime between the termination of old pods and the readiness of new pods
* Simpler than Rolling Update because there's no overlap between old and new versions at any point
* Useful when running two versions of the application simultaneously is not supported (e.g., database schema migration with breaking changes)

## How It Works

1. The Deployment Controller scales the old [[ReplicaSets]] to `0` replicas
2. All old Pods are terminated and removed
3. The new ReplicaSet is scaled up to the desired replica count
4. Traffic resumes once the new Pods pass their readiness probes

## Configuration

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: my-app:v2
```

## When to Use Recreate

* The application cannot run multiple versions simultaneously (e.g., singleton processes, shared state with breaking changes)
* Storage volumes can only be mounted by one pod at a time (e.g., `ReadWriteOnce` PVCs)
* The downtime window is acceptable and predictable
* Simpler rollout tracking is preferred over the complexity of rolling updates

## Downtime Estimation

The downtime window spans from when the last old Pod is terminated to when the first new Pod becomes `Ready`

* Actual downtime depends on: image pull time, container startup time, and readiness probe configuration
* Use `minReadySeconds` to add a stabilization delay after new pods become ready before the rollout is considered complete

## Rollback Behavior

Rollback under Recreate follows the same pattern: all new pods are terminated, then old pods are restored from the previous ReplicaSet.

## Recreate Downtime Window

The downtime window spans from when the last old Pod is terminated to when the first new Pod becomes `Ready`

* Actual downtime depends on: image pull time + container startup time + readiness probe initial delay
* Use `minReadySeconds` to add a stabilisation delay after pods become ready

```YAML
spec:
  minReadySeconds: 10    # Pod must be Ready for 10s before rollout proceeds
```

## Monitoring a Recreate Rollout

```Bash
kubectl rollout status deployment/my-deployment
