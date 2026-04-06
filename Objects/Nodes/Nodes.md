
Gracefully terminate Pods from a node and redeploy them onto a different node
```Bash
# Make the node unschedulable
kubectl cordon NODE_NAME

# Gracefully terminate all Pods from the node and the node becomes unschedulable
kubectl drain NODE_NAME [--ignore-daemonsets]

# Make the node schedulable 
kubectl uncordon NODE_NAME
```