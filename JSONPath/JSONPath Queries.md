# [[JSONPath]] Query Reference

A practical reference of common [[kubectl]] JSONPath queries for extracting cluster data.

## Nodes

```Bash
# All node names
kubectl get nodes -o jsonpath='{.items[*].metadata.name}'

# Node name + kernel version (tabular)
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"	"}{.status.nodeInfo.kernelVersion}{"
"}{end}'

# Node internal IPs
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"	"}{.status.addresses[?(@.type=="InternalIP")].address}{"
"}{end}'

# Nodes with a specific label
kubectl get nodes -l disktype=ssd -o jsonpath='{.items[*].metadata.name}'
```

## Pods

```Bash
# All pod names in a namespace
kubectl get pods -n NAMESPACE -o jsonpath='{.items[*].metadata.name}'

# Pod name + node assignment
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"	"}{.spec.nodeName}{"
"}{end}'

# Pod IPs
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"	"}{.status.podIP}{"
"}{end}'

# Container images in a pod
kubectl get pod MY_POD -o jsonpath='{.spec.containers[*].image}'

# Pods in Pending state
kubectl get pods -o jsonpath='{.items[?(@.status.phase=="Pending")].metadata.name}'
```

## Services

```Bash
# All service names and types
kubectl get svc -o jsonpath='{range .items[*]}{.metadata.name}{"	"}{.spec.type}{"
"}{end}'

# NodePort values
kubectl get svc MY_SVC -o jsonpath='{.spec.ports[*].nodePort}'
```

## Deployments

```Bash
# Deployment name + replica count
kubectl get deployments -o jsonpath='{range .items[*]}{.metadata.name}{"	"}{.spec.replicas}{"
"}{end}'

# Current image version
kubectl get deployment MY_DEPLOY -o jsonpath='{.spec.template.spec.containers[0].image}'
```

## Secrets and ConfigMaps

```Bash
# Decode a secret value
kubectl get secret MY_SECRET -o jsonpath='{.data.KEY}' | base64 -d

# List all secret keys
kubectl get secret MY_SECRET -o jsonpath='{.data}' | jq 'keys'
```

## Sorting Output

```Bash
kubectl get pods --sort-by='.metadata.creationTimestamp'
kubectl get pv --sort-by='.spec.capacity.storage'
```
