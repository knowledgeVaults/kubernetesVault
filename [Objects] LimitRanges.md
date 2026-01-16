# Kubernetes: Limit Ranges

A **limit range** is an object that specifies the **default resource limits and requests** for pods or containers within a namespace

![limit-range-visual-diagram](https://raw.githubusercontent.com/knowledgeVaults/imagesVault/main/kubernetes-limit-range.png)

<br>

# Common Deployment Methods

## Method 1

A limit range can be created and deployed using `kubectl`
```Bash
# Create default limit for RAM
kubectl create limitrange LIMIT_RANGE_NAME \
  --default-request=memory=VALUE \
  --namespace NAMESPACE

# Create default request for RAM
kubectl create limitrange LIMIT_RANGE_NAME \
  --default=memory=VALUE \
  --namespace NAMESPACE

# Create default limit for CPU
kubectl create limitrange LIMIT_RANGE_NAME \
  --default-request=cpu=VALUE \
  --namespace NAMESPACE

# Create default request for CPU
kubectl create limitrange LIMIT_RANGE_NAME \
  --default=cpu=VALUE \
  --namespace NAMESPACE
```

<br>

## Method 2

A limit range can be defined in a YAML file and then deployed using `kubectl`
```YAML
apiVersion: v1
kind: LimitRange
metadata:
  name: RESOURCE_NAME
  namespace: NAMESPACE
spec:
  limits:
  - default: 
      cpu: DEFAULT_CPU_LIMIT
      memory: DEFAULT_MEMORY_LIMIT
    defaultRequest: 
      cpu: DEFAULT_CPU_REQUEST
      memory: DEFAULT_MEMORY_REQUEST
    max:
      cpu: MAXIMUM_CPU
      memory: MAXIMUM_MEMORY
    min:
      cpu: MINIMUM_CPU
      memory: MINIMUM_MEMORY
    type: Container
```
```Bash
kubectl apply -f FILENAME.yaml
```
