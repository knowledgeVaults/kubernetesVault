
## Infrastructure-as-Code Approaches

### Imperative

The imperative approach gives the system step-by-steps instructions on how to create and modify resources

* The user manually controls every action

Example: *Deploying NGINX*
```Bash
# Create a deployment
kubectl create deployment nginx --image=nginx

# Scale the deployment
kubectl scale deployment nginx --replicas=3

# Expose the deployment
kubectl expose deployment nginx --port=80
```

### Declarative

The declarative approach describes the desired final state 

* The user defines the infrastructure in a configuration file (YAML) and lets the system figure out how to reach that state
* Kubernetes will constantly compare the resource's desired state with its actual state and make updates if needed to ensure that it's actual state matches its desired state

Example: *Deploying NGINX*
```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

```Bash
kubectl apply -f deployment.yaml
```