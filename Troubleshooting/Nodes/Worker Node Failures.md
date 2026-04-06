
Check node status
```Bash
kubectl get nodes
```

tmp
```Bash
kubectl describe node NODE_NAME
```

tmp
```Bash
top
```

tmp
```Bash
df -h
```

tmp
```Bash
sudo journalctl -u SERVICE_NAME
```

Check the kubelet certificates
```Bash
openssl x509 -in /var/lib/kubelet/WORKER_NODE.crt -text
```