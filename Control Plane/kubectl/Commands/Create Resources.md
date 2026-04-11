
Some `kubectl` Commands for creating Kubernetes resources imperatively or declaratively.

## Declarative (Recommended)

```Bash
# Create or update resources from a file
kubectl apply -f FILENAME.yaml

# Apply all manifests in a directory
kubectl apply -f ./manifests/

# Apply from stdin
cat pod.yaml | kubectl apply -f -

# Apply with dry-run validation
kubectl apply -f FILENAME.yaml --dry-run=server
```

## Imperative Creation

```Bash
# Create a Pod
kubectl run POD_NAME --image=IMAGE[:TAG] [-n NAMESPACE]

# Create a Deployment
kubectl create deployment NAME --image=IMAGE --replicas=3

# Create a Service (expose a deployment)
kubectl expose deployment NAME --port=80 --target-port=8080 --type=ClusterIP

# Create a ConfigMap
kubectl create configmap NAME --from-literal=KEY=VALUE
kubectl create configmap NAME --from-file=./config.properties

# Create a Secret
kubectl create secret generic NAME --from-literal=KEY=VALUE
kubectl create secret docker-registry NAME \
  --docker-server=REGISTRY --docker-username=USER --docker-password=PASS

# Create a Namespace
kubectl create namespace NAME

# Create a ServiceAccount
kubectl create serviceaccount NAME -n NAMESPACE

# Create a Role
kubectl create role NAME --verb=get,list --resource=pods -n NAMESPACE

# Create a RoleBinding
kubectl create rolebinding NAME --role=ROLE_NAME --user=USER -n NAMESPACE

# Create a ClusterRole
kubectl create clusterrole NAME --verb=get,list,watch --resource=nodes

# Create a ClusterRoleBinding
kubectl create clusterrolebinding NAME --clusterrole=ROLE_NAME --user=USER
```

## Generating Manifests (Dry Run)

```Bash
# Generate YAML without creating the resource
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
kubectl create deployment my-app --image=my-app:v1 --dry-run=client -o yaml > deploy.yaml
```

## From URL

```Bash
kubectl apply -f https://raw.githubusercontent.com/org/repo/main/manifest.yaml
```

## Job

```Bash
kubectl create job NAME --image=IMAGE -- /bin/sh -c "echo hello"
kubectl create cronjob NAME --image=IMAGE --schedule="*/5 * * * *" -- echo hello
```
