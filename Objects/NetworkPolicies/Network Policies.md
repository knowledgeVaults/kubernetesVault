
* Kubernetes is configured by default with an "All Allow" rule that allows all traffic from one resource to another within the cluster 
* Linked to one or more Pods 
* Enforced by the networking solution implemented in the Kubernetes cluster (Not all networking solutions support network policies)

NetworkPolicy
```YAML
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: NETWORK_POLICY_NAME
spec:
  # Not including the podSelector will allow all the pods in the namespace to access the resource rather than only the pods with the corresponding label
  podSelector:
    matchLabels:
      KEY: VALUE
  policyTypes:
  - {Ingress|Egress}
  ingress:
  - from:
    # Rule #1 (Multiple rules act as an OR operation and multiple conditions within a single rule act as an AND operation)
    - podSelector:
        matchLabels:
          KEY: VALUE
      namespaceSelectod:
        matchLabels:
          kubernetes.io/metadata.name: NAMESPACE
    # Rule #2
    - ipBlock:
        cidr: NETWORK_ADDRESS/SUBNET_RANGE
    ports:
    - protocol: TCP
      port: PORT_NUMBER
  egress:
  - to:
    - ipBlock:
        cidr: NETWORK_ADDRESS/SUBNET_RANGE
      ports:
      - protocol: TCP
        port: PORT_NUMBER
```

Pod
```YAML
[...]
labels:
  KEY: VALUE
```