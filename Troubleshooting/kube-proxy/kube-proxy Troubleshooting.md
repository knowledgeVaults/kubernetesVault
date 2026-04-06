
Check that kube-proxy is running
```Bash
kubectl get pods -n kube-system | grep kube-proxy
```

Check kube-proxy logs
```Bash
kubectl logs kube-proxy -n kube-system
```

Check if the config map is correctly defined and if the config file for running the kube-proxy binary is correct

Check if kube-proxy is running inside the container