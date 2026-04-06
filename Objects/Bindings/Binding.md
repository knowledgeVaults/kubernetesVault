
A `Binding` is a Kubernetes API object that's used to bind a Pod to a Node

```YAML
apiVersion:
kind: Binding
metadata:
  name: POD_NAME
target:
  apiVersion: v1
  kind: Node
  name: NODE_NAME
```

```Bash
```