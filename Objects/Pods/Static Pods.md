
* [[etcd]] will mirror the Pod but you can only modify it by modifying the local Pod definition file
* Not dependent on the Kubernetes control plane 
* The [[kubelet]] automatically restarts the Pods if something happens to them
* Ignored by the [[kube-scheduler]]

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

## What Are Static Pods

Static Pods are managed directly by the kubelet on a node without going through the [[kube-apiserver]] or scheduler

* Defined as YAML files in a directory on the node (default: `/etc/kubernetes/manifests/`)
* The kubelet watches this directory and starts/restarts pods from these files automatically
* Cannot be managed with `kubectl delete` — deleting the manifest file is the only way to stop them
* Control plane components (kube-apiserver, etcd, kube-scheduler, [[kube-controller-manager]]) run as Static Pods in [[kubeadm]] clusters

## Configuring Static Pod Path

```Bash
# In kubelet service flags
--pod-manifest-path=/etc/kubernetes/manifests

# Or in KubeletConfiguration file
staticPodPath: /etc/kubernetes/manifests
```

## Mirror Pods

The API server creates read-only **mirror pods** for Static Pods as they appear in `kubectl get pods` but cannot be deleted via kubectl

```Bash
kubectl get pods -n kube-system
# kube-apiserver-controlplane     ← mirror pod (Static Pod on control plane node)
```

## Finding Static Pod Location

```Bash
cat /var/lib/kubelet/config.yaml | grep staticPodPath
# staticPodPath: /etc/kubernetes/manifests

ls /etc/kubernetes/manifests/
