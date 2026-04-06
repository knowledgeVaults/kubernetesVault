
A `ServiceAccount` is an object that serves as an identity for non-human workloads so that they can authenticate to the API server and be authorized via RBAC

* Each service account is associated with a token that's used to authenticate the service account
* Kubernetes creates a service account in all namespaces by default that's automatically attached to each created resource by default
* Mounted to objects as a projected volume usually at `/var/run/secrets/kubernetes.io/serviceaccount`

## Tokens

* Bound to the life of the resource
* The kubelet automatically rotates the tokens or you can also create a token for external uses that aren't stored in any secrets

```Bash
kubectl create token TOKEN_NAME [--duration TIME]
```

Decode a token
```
jq -R 'split(".") | select(length > 0) | .[0],.[1] | @base64d | fromjson' <<< TOKEN
```

## tmp

```YAML
apiVersion: v1
kind: ServiceAccount
metadata:
  name: SERVICE_ACCOUNT_NAME
  namespace: NAMESPACE
autoMountServiceAccountToken: {true|false}
```

Specify a service account for Pods
```
apiVersion: v1
kind: Pod
metadata:
  name: POD_NAME
  namespace: NAMESPACE
spec:
  [...]
  serviceAccountName: SERVICE_ACCOUNT_NAME
  autoMountServiceAccountToken: {true|false}
```