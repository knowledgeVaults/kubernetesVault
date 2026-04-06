
* Can modify requests
* First mutating controllers then validating controllers

## Enable Admission Controllers

kube-apiserver.service
```text
ExecStart=/usr/local/bin/kube-apiserver \\
  [...] \\
  --enable-admission-plugins=ADMISSION_PLUGINS 
```

kube-apiserver.yaml
```YAML
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command
    - kube-apiserver
    - --[...]
    - --enable-admission-plugins=NodeRestriction
    
    image:
    name:
```