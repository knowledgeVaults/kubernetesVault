
```
image: REGISTRY/ACCOUNT_NAME/IMAGE_REPOSITORY
```

* The default registry is docker.io
* library is the name of the default account where Docker's images are stored (These images promote best practices)

## Private Repository

```Bash
docker login PRIVATE_REGISTRY.io
docker run PRIVATE_REGISTRY.io/ACCOUNT_NAME/IMAGE_REPOSITORY
```

Create secret
```Bash
kubectl create secret docker-registry SECRET_NAME --docker-server= PRIVATE_REGISTRY.io --docker-username= REGISTRY_USER --docker-password= REGISTRY_PASSWORD --docker-email= REGISTRY_USER_EMAIL
```

```YAML
apiVersion: v1
kind: Pod
metadata:
  name POD_NAME
  namespace: NAMESPACE
spec:
  containers:
  - name: CONTAINER_NAME
    image: PRIVATE_REGISTRY.io/ACCOUNT_NAME/IMAGE_REPOSITORY
  imagePullSecretes:
  - name: SECRET_NAME
```