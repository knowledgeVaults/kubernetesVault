
Check node status
```Bash
kubectl get nodes
```

View service logs
```Bash
sudo journalctl -u kube-apiserver
```

View Pod logs
```Bash
kubectl get pods -n NAMESPACE
```