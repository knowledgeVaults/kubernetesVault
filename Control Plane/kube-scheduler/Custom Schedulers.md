**Custom Schedulers** are alternative scheduler implementations that run alongside or instead of the default [[kube-scheduler]] to implement specialised scheduling logic

* Kubernetes supports running multiple schedulers simultaneously where each Pod chooses its scheduler via `spec.schedulerName`
* A custom scheduler must implement: watching for unscheduled Pods, selecting a node, and writing a `Binding` to the [[kube-apiserver]]
* Can be built using the **Scheduling Framework** (plugin system), as a completely independent binary, or as a wrapper around the default scheduler

## Deploying a Custom Scheduler

A custom scheduler is typically deployed as a [[Deployments]] in the cluster

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-custom-scheduler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      component: my-custom-scheduler
  template:
    metadata:
      labels:
        component: my-custom-scheduler
    spec:
      serviceAccountName: my-scheduler-sa
      containers:
        - name: scheduler
          image: my-org/custom-scheduler:v1.0.0
          command:
            - /usr/local/bin/my-scheduler
            - --config=/etc/scheduler/config.yaml
            - --leader-elect=false
```

## Selecting a Custom Scheduler for a Pod

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  schedulerName: my-custom-scheduler
  containers:
    - name: nginx
      image: nginx
```

If the named scheduler is not running, the Pod stays in `Pending` with a `FailedScheduling` event.

## [[RBAC]] for Custom Schedulers

A custom scheduler needs permission to read Pods, Nodes, and write Bindings/Events

```Bash
kubectl create clusterrolebinding my-scheduler-binding \
  --clusterrole=system:kube-scheduler \
  --serviceaccount=kube-system:my-scheduler-sa
```

## Debugging Custom Scheduler Issues

```Bash
kubectl get events -n NAMESPACE --field-selector reason=FailedScheduling
kubectl logs -n kube-system deploy/my-custom-scheduler
kubectl describe pod MY_POD | grep -A5 Events
```
