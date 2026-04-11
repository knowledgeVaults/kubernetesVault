
A [[Service Accounts]] is an object that serves as an identity for non-human workloads so that they can authenticate to the [[kube-apiserver]] and be authorized via [[RBAC]]

* Each service account is associated with a token that's used to authenticate the service account
* Kubernetes creates a service account in all namespaces by default that's automatically attached to each created resource by default
* Mounted to objects as a projected volume usually at `/var/run/secrets/kubernetes.io/serviceaccount`

## Tokens

* Bound to the life of the resource
* The [[kubelet]] automatically rotates the tokens or you can also create a token for external uses that aren't stored in any secrets

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

## RBAC Integration

A ServiceAccount has no permissions by default because it binds it to a Role or [[Cluster Roles]] to grant access

```Bash
kubectl create role app-role --verb=get,list --resource=configmaps -n default
kubectl create rolebinding app-binding --role=app-role --serviceaccount=default:my-app-sa
```

## Disabling Automatic Token Mounting

Disable automatic token mounting for pods that don't need API access

```YAML
# On the ServiceAccount (affects all pods using this SA)
automountServiceAccountToken: false

# On the Pod (per-pod override)
spec:
  automountServiceAccountToken: false
```

## Checking a ServiceAccount's Permissions

```Bash
kubectl auth can-i list pods --as=system:serviceaccount:default:my-app-sa
kubectl auth can-i --list --as=system:serviceaccount:default:my-app-sa
```

## ServiceAccount for [[Argo CD]] / Prometheus

Common pattern: create a dedicated ServiceAccount with a ClusterRole for cluster-wide read access

```Bash
kubectl create serviceaccount prometheus-sa -n monitoring
kubectl create clusterrolebinding prometheus-binding \
  --clusterrole=view \
  --serviceaccount=monitoring:prometheus-sa
```
