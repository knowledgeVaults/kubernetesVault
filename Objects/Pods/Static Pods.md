
* etcd will mirror the Pod but you can only modify it by modifying the local Pod definition file
* Not dependent on the Kubernetes control plane 
* The kubelet automatically restarts the Pods if something happens to them
* Ignored by the kube-scheduler

```Conf
ExecStart=/usr/local/bin/kubelet \\
  [...]
  --pod-manifest-path=DIRECTORY_PATH
  [...]
```

or 

```Conf
ExecStart=/usr/local/bin/kubelet \\
  [...]
  --config=KUBECONFIG_FILE_PATH
  [...]
```
```Conf
[...]
staticPodPath: DIRECTORY_PATH
```

View the Pods
```Bash
docker ps
```

Find how many static Pods exist in the cluster
```Bash
kubectl get pods -A -o jsonpath='{range .items[?(@.metadata.annotations.kubernetes\.io/config\.source=="file")]}{.metadata.name}{"\n"}{end}' | wc -l
```

Check what Pods are deployed as static Pods
```Bash
kubectl get pods -A -o jsonpath='{range .items[?(@.metadata.annotations.kubernetes\.io/config\.source=="file")]}{.metadata.namespace}{" "}{.metadata.name}{"\n"}{end}'
```

```Bash
cat /var/lib/kubelet/config.yaml | grep staticPodPath
```

```Bash
ls /etc/kubernetes/manifests | wc -l
```

```Bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep image
```