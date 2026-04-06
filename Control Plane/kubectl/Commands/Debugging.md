
### YAML

Generate a YAML manifest for a Pod without actually creating it
```YAML
kuebctl run POD_NAME --image=IMAGE --dry-run={client|server} [-o yaml]
```

### Logs

Fetch container logs
```Bash
kubectl logs POD_NAME
```

### RCE

Connect to a Pod's shell
```Bash
kubectl exec -it POD_NAME
```