
Check the events of a Pod
```Bash
kubectl describe pod POD_NAME -n NAMESPACE
```

Check the logs of a Pod
```Bash
# Check the logs of a Pod
kubectl logs POD_NAME -n NAMESPACE 

# Check the logs of the previous Pod
kubectl logs POD_NAME -n NAMESPACE --previous
```