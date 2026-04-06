
tmp

* The control plane can have more than one scheduler 
* Each scheduler will needs its own configuration file

## Deploying Custom Schedulers

### Service

tmp
```Bash
wget https://storage.googleapis.com/kubernetes-release/
```

tmp
```YAML
apiVersion:
kind:
profiles:
- schedulerName: SCHEDULER_NAME
```

tmp
```
# scheduler.service
ExecStart=/usr/local/bin/kube-scheduler \\
  --config=PATH_TO_SCHEDULER_YAML
```

### Pod

tmp
```YAML
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
- schedulerName: SCHEDULER_NAME
leaderElection:
  leaderElect: true
  resourceNamespace: kube-system
  resourceName: SCHEDULER_NAME
```

tmp
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: POD_NAME
  namespace: kube-system
spec:
  containers:
  - command:
      - kube-scheduler
      - --address=127.0.0.1
      - --kubeconfig=/etc/kubernetes/scheduler.conf
      - --config=PATH_TO_SCHEDULER_YAML  
        image: SCHEDULER_IMAGE
        name: kube-scheduler
```

## Using Custom Schedulers

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: POD_NAME
spec:
  containers:
  - name: CONTAINER_NAME
    image: IMAGE_NAME
  schedulerName: SCHEDULER_NAME
```