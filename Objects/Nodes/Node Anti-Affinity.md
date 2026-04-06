
Node anti-affinity is a rule that tells the scheduler which nodes to avoid placing pods on

* Ensures that pods don't run on the same node as other pads that match specific labels 

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: DEPLOYMENT_NAME
spec:
  replicas: NUMBER
  selector:
    KEY: VALUE
  template:
    metadata:
      labels:
        KEY: VALUE
    spec:
      affinity:
        podAntiAffinity:
          AFFINITY_TYPE:
            - labelSelector:
                matchExpressions:
                  - key: KEY
                    operator: OPERATOR
                    values:
                    - VALUE
              topologyKey: "kubernetes.io/hostname"
      containers:
        - name: CONTAINER_NAME
          image: IMAGE[:TAG]
          ports:
        - containerPort: PORT_NUMBER
```

## Node Anti-Affinity Types

### Required Anti-Affinity

`requiredDuringSchedulingIgnoredDuringExecution` is a strict requirement where a Pod cannot run on a node if another Pod with the same label is already running on the node

### Preferred Anti-Affinity

`preferredDuringSchedulingIgnoredDuringExecution` is a preference where the scheduler will try not to place a Pod on a node that already has a Pod with the same label running on it

* If no other nodes are available then it will place it on a node that's already running a Pod with the same label

```YAML
affinity:
  podAntiAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 100
      podAffinityTerm:
        labelSelector:
          matchLabels:
            app: web
        topologyKey: kubernetes.io/hostname
```

* `weight` tells the scheduler how strongly it should prefer this rule compared to other preferred rules  (The value ranges between 1 and 100 where the lower the weight the weaker the preference)