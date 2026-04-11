
* Kubernetes is configured by default with an "All Allow" rule that allows all traffic from one resource to another within the cluster 
* Linked to one or more Pods 
* Enforced by the networking solution implemented in the Kubernetes cluster (Not all networking solutions support network policies)

[[Network Policies]]
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

## Default Deny Pattern

The recommended baseline is a default-deny-all policy per namespace, then selectively allowing required flows

```YAML
# Default deny all ingress and egress in a namespace
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  namespace: production
spec:
  podSelector: {}    # Applies to all pods in namespace
  policyTypes:
    - Ingress
    - Egress
```

## Allow Specific Traffic

```YAML
# Allow ingress from pods with label app=frontend, only on port 8080
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

## Important Notes

* Network Policies are only enforced if the CNI plugin supports them (Calico, Cilium, Weave)
* Policies are **additive** since multiple policies matching the same pod combine with OR logic
* An empty `podSelector: {}` matches all pods in the namespace
* `namespaceSelector` selects based on namespace labels, not names (use `kubernetes.io/metadata.name` label for name-based selection)

## Commands

