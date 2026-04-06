
Priority classes define the importance of Pods and allow the scheduler to decide which Pods should run first and ones should be evicted first when the cluster lacks resources

* Defined using a `PriorityClass` object
* The scheduler will always prioritize higher priority workloads (Kubernetes components always get the higher priority)

Create a priority class
```YAML
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: PRIORITY_CLASS_NAME
value: NUMBER
globalDefault: {true|false}
description: "DESCRIPTION"
preemptionPolicy: 
```

Have a Pod use a priority class
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: POD_NAME
spec:
  priorityClassName: PRIORITY_CLASS_NAME
  containers:
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
```

List all the priority classes
```Bash
kubectl get priorityclass
```

Describe a priority class
```Bash
kubectl describe priorityclass PRIORITY_CLASS
```