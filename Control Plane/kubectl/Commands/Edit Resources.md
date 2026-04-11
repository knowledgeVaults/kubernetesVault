
Some `kubectl` Commands for modifying, scaling, and patching Kubernetes resources.

## Live Edit

```Bash
# Open resource in $EDITOR (vi by default)
kubectl edit RESOURCE RESOURCE_NAME [-n NAMESPACE]
kubectl edit deployment my-deployment
kubectl edit pod my-pod   # Limited fields editable on running pods
```

## Apply / Replace

```Bash
# Declarative update (preferred)
kubectl apply -f FILENAME.yaml

# Forced replace (delete and recreate)
kubectl replace --force -f FILENAME.yaml
```

## Patch

```Bash
# JSON Merge Patch
kubectl patch deployment NAME -p '{"spec":{"replicas":5}}'

# Strategic Merge Patch (default)
kubectl patch pod NAME --patch '{"spec":{"containers":[{"name":"nginx","image":"nginx:1.25"}]}}'

# JSON Patch
kubectl patch deployment NAME --type=json \
  -p '[{"op":"replace","path":"/spec/replicas","value":3}]'
```

## Scale

```Bash
kubectl scale deployment NAME --replicas=5
kubectl scale replicaset NAME --replicas=3 -n NAMESPACE
```

## Set Image

```Bash
kubectl set image deployment/NAME CONTAINER=IMAGE:TAG
kubectl set image deployment/my-app app=my-app:v2
```

## Label and Annotate

```Bash
# Add or update label
kubectl label nodes NODE_NAME env=production
kubectl label pod POD_NAME app=frontend

# Remove label
kubectl label nodes NODE_NAME env-
kubectl label pod POD_NAME app-

# Add annotation
kubectl annotate pod POD_NAME description="my pod"
```

## [[Taints]] Nodes

```Bash
# Add taint
kubectl taint node NODE_NAME key=value:NoSchedule

# Remove taint
kubectl taint node NODE_NAME key=value:NoSchedule-
```

## Rollout Commands

```Bash
kubectl rollout status deployment/NAME
kubectl rollout history deployment/NAME
kubectl rollout undo deployment/NAME
kubectl rollout pause deployment/NAME
kubectl rollout resume deployment/NAME
```

## [[Config Maps]] / [[Secrets]] Update

```Bash
kubectl create configmap NAME --from-literal=KEY=NEW_VALUE --dry-run=client -o yaml | kubectl apply -f -
```

## ConfigMap / Secret Update

```Bash
kubectl create configmap NAME --from-literal=KEY=NEW_VALUE --dry-run=client -o yaml | kubectl apply -f -
```
