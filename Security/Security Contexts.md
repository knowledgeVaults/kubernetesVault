
* Configuring security settings at the Pod level will have the settings carried over to all the containers within that Pod
* Configuring security settings at the container level will override the security settings from the Pod level

Apply a security context to a Pod
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: POD_NAME
  namespace: NAMESPACE
spec:
  securityContext:
    runAsUser: USER_ID
  containers:
    - name: CONTAINER_NAME
      image: IMAGE
      command: ["COMMAND"]
```

Apply a security context on the container level
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: POD_NAME
  namespace: NAMESPACE
spec:
  containers:
    - name: CONTAINER_NAME
      image: IMAGE
      command: ["COMMAND"]
      securityContext:
        runAsUser: USER_ID
        # Add additional capabilities
        capabilities:
          add: ["CAPABILITY]
```