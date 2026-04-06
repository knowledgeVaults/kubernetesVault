
Edit live object
```Bash
kubectl edit OBJECT OBJECT_NAME
```

Scale replicas
```Bash
kubectl scale OBJECT OBJECT_NAME --replicas=NUMBER_OF_REPLICAS
```

## Nodes

Add a label to a node
```Bash
kubectl label nodes NODE_NAME LABEL=VALUE 
```

Remove a label from a node
```Bash
kubectl label nodes NODE_NAME LABEL-
```