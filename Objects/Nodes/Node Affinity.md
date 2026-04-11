
[[Node Affinity]] is a rule that tells the scheduler which nodes a pod is allowed to run on based on node labels

* Allows certain pods to run on specific nodes

```YAML
apiVersion: v1
kind: Pod
metadata:
spec:
  affinity:
    nodeAffinity:
```

```Bash
kubectl get nodes --show-labels
```

## Node Affinity Types

### Required Node Affinity 

`requiredDuringSchedulingIgnoreDuringExecution` is a strict requirement where a Pod will only be scheduled on a node that matches the rule

* If no nodes match this rule then the Pod will stay pending

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: OBJECT_NAME
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: KEY
            operator: OPERATOR
            values:
            - VALUE
  containers:
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
```

### Preferred Node Affinity

`preferredDuringSchedulingIgnoredDuringExecution` is a preference where the scheduler will try to place the Pod on a matching node  

* If no matching nodes are available then it will schedule it on another available node

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: OBJECT_NAME
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: KEY
            operator: OPEARTOR
            values:
            - VALUE
  containers:
  - name: CONTAINER_NAME
    image: IMAGE[:TAG]
```

## Node Affinity Operators

* `In` instructs the scheduler that the node label value must match one of the listed values
* `NotIn` instructs the scheduler that the node label value must not match the listed values
* `Exists` instructs the scheduler that the label key must exist
* `DoesNotExist` instructs the scheduler that the label key must not exist
* `Gt` instructs the scheduler that the label value must be greater than
* `Lt` instructs the scheduler that the label value must be less than
