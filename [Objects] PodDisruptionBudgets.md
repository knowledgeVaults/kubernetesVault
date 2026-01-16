# Kubernetes: Pod Disruption Budgets

A **pod disruption budget** is an object that helps limit voluntary disruptions to pods within a node

* Ensures application availability during voluntary disruptions
* Prevents having too many pods from being disrupted at the same time
* Doesn't protect against involuntary disruptions

![pod-disruption-budget-visual-diagram](https://raw.githubusercontent.com/knowledgeVaults/imagesVault/main/kubernetes-pod-disruption-budget.png)

<br>

# Common Deployment Methods

## Method 1

A PodDisruptionBudget can be created and deployed using `kubectl`
```Bash
kubectl create poddisruptionbudget PDB_NAME \
  --selector=LABEL=VALUE \
  --min-available=VALUE \
  --max-unavailable=VALUE
```

<br>

## Method 2

A PodDisruptionBudget can be defined using a YAML file and then deployed using `kubectl`
```YAML
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: OBJECT_NAME
spec:
  minAvailable: NUMBER_OR_PERCENTAGE 
  maxUnavailable: NUMBER_OR_PERCENTAGE 
  selector:
    matchLabels:
      LABEL: VALUE
```
```Bash
kubectl apply -f FILENAME.yaml
```