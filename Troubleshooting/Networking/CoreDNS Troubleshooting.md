
## CrashLoopBackOff 

Modify the CoreDNS deployment to set `allowPrivilegeEscalation` to `true`
```Bash
kubectl -n kube-system get deployment coredns -o yaml | sed 's/allowPrivilegeEscalation: false/allowPrivilegeEscalation: true/g' | kubectl apply -f
```

- Add the following to your kubelet config yaml: **_resolvConf: _** This flag tells **_kubelet_** to pass an alternate **_resolv.conf_** to Pods. For systems using **systemd-resolved**, **_/run/systemd/resolve/resolv.conf_** is typically the location of the **_“real” resolv.conf_**, although this can be different depending on your distribution.
    
- Disable the local DNS cache on host nodes, and restore **_/etc/resolv.conf_** to the original.
    
- A quick fix is to edit your **Corefile**, replacing forward **_. /etc/resolv.conf_** with the IP address of your upstream DNS, for example forward **. 8.8.8.8**. But this only fixes the issue for **CoreDNS**, **_kubelet_** will continue to forward the invalid **_resolv.conf_** to all default dnsPolicy Pods, leaving them unable to resolve DNS.

Check if the kube-dns service has valid endpoints
```Bash
kubectl -n kube-system get ep kube-dns

# If there are no endpoints for the service, inspect the service and make sure it uses the correct selectors and ports
```