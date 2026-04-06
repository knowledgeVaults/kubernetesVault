
* Runs as a process on every worker node within a Kubernetes cluster
* It's job is to look for new services and every time a new service is created, it'll create the appropriate rules on each node to forward traffic to those services to the backend pods
* Creates an iptable rule to forward traffic heading to the IP of the service to the IP of the actual pod
* There shouldn't be a case where a Pod and a Service is assigned the same IP address or are within the same IP range

Set the kube-proxy mode
```Bash
kube-proxy --proxy-mode {userspace|iptables|ipvs}
```

View Service cluster IP range
```Bash
ps aux | grep kube-api-server
```

tmp
```Bash
iptables -L -t nat | grep SERVICE_NAME
```

| kube-proxy Mode | Description |
| --------------- | ----------- |
| userspace       |             |
| iptables        |             |
| ipvs            |             |
